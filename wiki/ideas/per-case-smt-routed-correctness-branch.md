---
title: "Per-case SMT-routed correctness branch (golden-seeded KB)"
slug: per-case-smt-routed-correctness-branch
status: proposed
origin: "Design extension to [[decomposed-reference-based-golden-evaluator]]'s Stage 2 Correctness. The current Stage 2 uses three-way NLI (entail / contradict / neutral) against y*. This catches surface-level disagreement but cannot verify whether a rule the agent cites is correctly *applied* — only whether the agent's sentence textually matches something in y*. For rule-heavy outputs (underwriting, compliance, legal) this is insufficient: an agent can cite the right rule code with a surface-plausible sentence and still misapply the rule's conditions. Delegating rule-application verification to a symbolic solver provides faithfulness by construction for that subset of claims."
origin_gaps: [llm-solver-integration-improves-faithful-reasoning, dsl-mediated-autoformalization-improves-translation-robustness, policy-rules-can-be-formalized-as-smt-constraints, minimal-modification-smt-repair-enables-actionable-compliance-diagnostics]
tags: [golden-evaluator, neurosymbolic, smt, z3, correctness-verification, rule-compliance, self-learning-phase-2, design-extension]
domain: NLP
priority: 4
pilot_result: ""
failure_reason: ""
linked_experiments: []
date_proposed: 2026-04-15
date_resolved: ""
---

## Motivation

[[decomposed-reference-based-golden-evaluator]]'s Stage 2 Correctness runs three-way [[natural-language-inference]] (NLI) of each agent bullet $b_{s,j}$ against the golden section $B^*_s$. For **empirical** claims ("sources describe the company as commercial"), this is the right primitive — NLI measures textual alignment and we have no richer ground truth. For **rule-application** claims ("HS 21 38 is mandatory when operation = residential construction", "HC 23 89 is a valid alternative to HS 30 09 for anti-stacking"), NLI is structurally insufficient: it can verify that the agent's sentence aligns with a golden sentence, but it cannot verify that the rule's *conditions* are actually satisfied by the case facts.

Two concrete failure modes NLI silently misses:

1. **Correct rule code, wrong application.** Agent says "HS 21 38 does not apply here because the operation is predominantly commercial". NLI against a golden that says "HS 21 38 applies because residential exposure is material" registers as `contradict` — but this is the easy case. Harder: if $y^*$ doesn't directly address the commercial-predominance framing, NLI lands on `neutral`, and the misapplication is not flagged at all.
2. **Valid-alternative rule substitution.** Agent cites `HC 23 89` (Continuous/Progressive Injury Exclusion) where $y^*$ cites `HS 30 09` (Anti-Stacking Endorsement). If both are valid responses to the same exposure per UW guidelines, this is correct substitution, but naive NLI treats the different citations as a mismatch.

Both are rule-application questions. The symbolic-solver literature provides the tools to check them: [[llm-solver-integration-improves-faithful-reasoning]] establishes the general gain of neurosymbolic pipelines over LLM-only reasoning; [[policy-rules-can-be-formalized-as-smt-constraints]] establishes that policy-style rules can be SMT-encoded and checked at runtime; [[dsl-mediated-autoformalization-improves-translation-robustness]] provides the practical pattern for making the LLM→SMT translation robust; [[minimal-modification-smt-repair-enables-actionable-compliance-diagnostics]] provides the diagnostic output format.

The engineering blocker for applying these to UW evaluation is the cost of formalising the full UW rule corpus offline. A golden-seeded per-case KB sidesteps that cost: only formalise the rules each case actually touches, amortising nothing, paying exactly for what is used.

## Hypothesis

A **per-case SMT-routed correctness branch** — where the KB is constructed lazily from $y^*$'s cited rules (with expansion to resolve agent citations that aren't in $y^*$) — will:

1. **Catch rule-application failures that NLI misses**, especially misapplied conditions and valid-alternative substitutions, at measurable rate on the calibration set
2. **Produce actionable per-claim diagnostics** (via minimal-modification output) rather than binary verdicts, enabling sharper PR-optimisation gradients than NLI's three-way classification
3. **Incur bounded engineering cost** — per-case KB size is O(5–15 rules) rather than O(hundreds), making the architecture tractable without a full offline rule-corpus formalisation project
4. **Not cannibalise NLI coverage** — the router classifies each claim as formalisable (→ SMT) or empirical (→ NLI); both branches coexist and cover disjoint claim populations

The core claim: **for the rule-application subset of agent claims, SMT is strictly more informative than NLI against $y^*$, and the golden-seeded KB construction makes this tractable without a full-corpus formalisation investment.**

## Approach sketch

### Stage A — Claim classifier (new)

For each agent bullet $b_{s,j}$, classify as:

- **Rule-application claim**: references a rule code, makes an applies/does-not-apply statement, asserts a mandatory/prohibited action. Signals: presence of `[FORM-CODE]` citations, verbs like "applies", "is required", "must use", "excludes", "mandatory". Route to SMT branch.
- **Empirical claim**: describes operations, loss history, company characteristics, source attributions. Route to existing [[natural-language-inference]] branch (Stage 2 of parent idea).
- **Ambiguous**: route to both, report disagreement as a diagnostic.

Classifier can be LLM-based (few-shot) with a regex pre-filter on citation patterns. Classifier accuracy is itself a calibration metric.

### Stage B — Per-case KB construction

**Seed from $y^*$:**

1. Extract all rule references from $y^*$ (codes like `HS 21 38`, `UW42427`, `HC 23 89`, and endorsement names).
2. For each reference, look up the rule's text in the UW rule corpus (static lookup, no LLM).
3. Autoformalize each rule into SMT-LIB constraints via DSL-mediated pipeline (see Stage C). Produce the initial KB $\Phi_{\text{KB}}^{(y^*)}$.

**Expand from $\hat{y}$:**

4. Extract rule references from $\hat{y}$. For each reference not already in $\Phi_{\text{KB}}^{(y^*)}$:
   - **Resolves in UW corpus** → formalize and add to KB. Flag: "expanded from agent citation".
   - **Does not resolve** → classifier emits a **hallucination flag** for this bullet (code doesn't exist).

5. Also add classification-schema constraints (operation types, exposure classes) referenced by any formalised rule. These are static schema elements, not per-case.

Final per-case KB: $\Phi_{\text{KB}}^{(\text{case})} = \Phi_{\text{KB}}^{(y^*)} \cup \Phi_{\text{KB}}^{(\hat{y}\text{-resolved})} \cup \Phi_{\text{schema}}$.

### Stage C — Autoformalization (DSL-mediated)

Per [[dsl-mediated-autoformalization-improves-translation-robustness]]: design a UW-tailored JSON DSL with sorts for:

- **OperationType** (residential-new-build, residential-remodel, multi-family, commercial, window-door-installation, ...)
- **EndorsementCode** (`HS 21 38`, `HS 30 09`, `HC 23 89`, ...)
- **ExposureClass** (high-exposure, restricted, DNW, ...)
- **Condition** (predicates over the above)
- **Obligation** (mandatory / prohibited / permitted rule conclusions)

LLM emits DSL representing the looked-up rule text; a deterministic typed interpreter converts DSL → SMT-LIB for Z3. Type errors surface at the interpreter, not the solver, producing cleaner error messages for the self-refinement loop.

### Stage D — SMT check per claim

For each rule-application bullet $b_{s,j}$:

1. Extract the claim's assertion in DSL form (what rule, what operation context, what conclusion).
2. Pose the check: given $\Phi_{\text{KB}}^{(\text{case})}$ and the facts extracted from $y^*$'s case context, does the claim's assertion hold?
3. Z3 returns `satisfiable` (assertion consistent), `unsatisfiable` (assertion contradicts the formalised rules), or runs into modelling failure.
4. On `unsatisfiable`, compute **minimal modification** (per [[minimal-modification-smt-repair-enables-actionable-compliance-diagnostics]]): which fact or claim would need to change to restore consistency? This localises the specific failure.

### Stage E — Aggregation with [[natural-language-inference]] branch

Correctness per section now has two contributions:

$$\text{Correctness}_s = \text{combine}\left( \text{Correctness}^{\text{NLI}}_s, \text{Correctness}^{\text{SMT}}_s \right)$$

where both sub-scores are importance-weighted over their respective claim populations. Concrete combine: union the verdicts (a claim fails correctness if *either* branch flags it). Report both sub-scores on the calibration card alongside the combined figure — a mismatch between the two is itself diagnostic (NLI-passes but SMT-fails means the agent surface-aligned with $y^*$ but misapplied a rule).

## Expected outcome

- **New failure modes surfaced**: rule misapplications and valid-alternative substitutions that NLI silently passed or flagged as neutral. Pilot success condition: SMT branch surfaces at least one category of misapplication that NLI did not flag on ≥10% of calibration-set bullets.
- **Per-case KB cost bounded and predictable**: empirical distribution of rules-per-case on the calibration set; expect a long-tail with most cases touching ≤10 rules. The marginal cost of adding SMT to an evaluation is ~10 rule formalisations, cacheable across $\hat{y}$ variants for the same $y^*$.
- **DSL artifact reusability**: the UW DSL, once designed, is static across cases. DSL design is the main one-time cost; per-case formalisation runs against that DSL without re-designing.
- **Richer calibration card**: adds `SMT-branch κ vs UW`, `NLI-SMT disagreement rate`, `hallucinated-rule-code count`, `valid-alternative-substitution count`, `autoformalization resolution rate` to the existing [[decomposed-reference-based-golden-evaluator]] calibration card.
- **Sharper PR gradient** on rule-application claims — minimal-modification output tells PR which specific rule applications to correct, rather than "correctness dropped by 0.05 on this bullet".

## Worked scenarios (against the user's example)

Using the example from the idea's conversation history:

**Scenario 1 — golden cites `HS 21 38`, agent cites `HS 21 38/XL2324`.**
- Classifier: both are rule-application claims. Route to SMT.
- KB seed: `HS 21 38` formalised from corpus.
- KB expand: `XL2324` looked up. Resolves → formalised.
- SMT check: "agent asserts HS 21 38 *or* XL2324 applies". Z3 verifies against formalised rules + case exposure facts. If `XL2324` is a valid alternative per UW guidelines, SMT returns consistent.
- NLI branch: likely flags `neutral` on `XL2324` (not in $y^*$).
- Aggregate: SMT passes, NLI neutral → resolved in agent's favour with diagnostic ("valid alternative via XL2324").

**Scenario 2 — golden cites multi-family as high-exposure; agent says high-end residential.**
- Classifier: this is a categorical/empirical claim, not a rule-application claim. Route to **NLI** (not SMT). SMT branch is not responsible for this failure; the grounding-drift diagnostic already added in [[decomposed-reference-based-golden-evaluator]] is.

**Scenario 3 — agent cites `UW99999` (hypothetical hallucinated code).**
- Classifier: rule-application claim.
- KB expand: `UW99999` does not resolve in corpus.
- Router emits hallucination flag on this bullet; do not pass to SMT.
- Counted as a rule-citation hallucination in the calibration card; aggregate correctness treats as a contradict.

## Risks

> [!warning] Risks
> - **Classifier accuracy bottleneck**: an empirical claim misclassified as rule-application can produce spurious SMT verdicts (the SMT check is ill-posed for non-formalisable content). Mitigation: calibrate the classifier against UW-annotated gold labels on a ≥50-bullet sample; track classifier confusion matrix on the calibration card. Ambiguous claims route to both branches and use disagreement as a diagnostic.
> - **Autoformalization accuracy** (the canonical neurosymbolic failure mode): [[neuro-symbolic-compliance-integrating-llms-smt]] reports 86.2% SMT generation correctness on a specialist legal domain — so expect ~10–15% translation error rate on UW rules. Mitigations: (a) UW-expert review of the formalised KB for each cited rule on first encounter; cache formalisations so review cost is one-time per rule; (b) self-refinement loop on interpreter errors (per [[logic-lm-empowering-llms-symbolic-solvers]]); (c) UW-audited gold SMT for a sample of common rules as a calibration anchor.
> - **Rule-corpus drift**: the UW rule corpus evolves; a formalisation cached from an old version may mis-encode a revised rule. Mitigation: formalisation cache keyed by `(rule_code, corpus_version_hash)`; invalidate on corpus update.
> - **Citation resolution gaps**: $y^*$ may cite codes that don't resolve in the corpus (stale corpus, typo, proprietary form not yet indexed). Mitigation: report **resolution rate** per case as a calibration-card metric; below a threshold the SMT branch is underspecified and correctness for that case should be flagged.
> - **DSL expressiveness vs complexity trade-off**: too-narrow DSL forces rules into expressions that don't fit (forces the autoformalization LLM to improvise); too-broad DSL reintroduces direct-SMT brittleness. Mitigation: iterative DSL design with UW expert review against a starter set of 20 high-frequency rules; expand only when a rule cannot be expressed.
> - **Engineering investment**: DSL design + interpreter + classifier + autoformalization pipeline is not small. Mitigation: pilot on a narrow subset (e.g. just mandatory-endorsement rules, ~10 rule codes) before expanding. Verify Expected Outcome #1 (new failure modes surfaced) on the pilot before scaling.
> - **Compute cost per evaluation**: adds ~10–30 autoformalization + SMT calls per $\hat{y}$, on top of the existing NLI budget. Mitigation: cache autoformalizations per rule code; cache golden-side KB construction per $y^*$ (same KB across all $\hat{y}$ for that case).

> [!success] Pilot results
> *(not yet run)*

> [!info] Lessons learned
> *(not yet available)*
