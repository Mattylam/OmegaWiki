---
title: "Decomposed reference-based Golden Evaluator"
slug: decomposed-reference-based-golden-evaluator
status: proposed
origin: "Design critique of the holistic 0-5 rubric GE: (1) the setting is reference-based (golden output y* exists and is pre-validated as solving the underlying issue) but the rubric treats it as reference-free; (2) the three criteria (completeness, correctness, conciseness) have fundamentally different natures that are conflated by a single rubric; (3) rubric-based pointwise scoring exhibits position bias over score options (see [[rubric-based-pointwise-scoring-is-implicitly-multi-choice]]); (4) issue-resolution is already encoded in y* by construction and the three per-criterion metrics address it directly — no separate gate is needed."
origin_gaps: [rubric-based-pointwise-scoring-is-implicitly-multi-choice, llm-judges-exhibit-systematic-multi-category-biases, correlation-is-insufficient-for-llm-judge-evaluation, decomposing-generation-into-atomic-facts-enables-fine-grained-evaluation, factuality-metrics-are-sensitive-to-decomposition-method, decomposer-quality-can-be-measured-automatically]
tags: [golden-evaluator, reference-based-evaluation, decomposition, self-learning-phase-2, prerequisite, design-alternative]
domain: NLP
priority: 5
pilot_result: ""
failure_reason: ""
linked_experiments: []
date_proposed: 2026-04-15
date_resolved: ""
---

## Motivation

The current Golden Evaluator design in [[golden-evaluator-calibration-baseline]] uses a single LLM-as-judge to score the agent output $\hat{y}$ on three criteria (completeness, correctness, conciseness) with a 0–5 ordinal rubric per criterion. That design has three structural weaknesses that compound:

1. **Under-used reference signal.** The setup provides a golden output $y^*$, yet a holistic-rubric judge operates effectively reference-free — it still has to internalise an absolute 0–5 scale. Most of the failure modes catalogued in [[llm-judge-bias-taxonomy]] (verbosity, sentiment, self-preference, calibration drift) are artefacts of reference-free judgment.

2. **Criterion-nature conflation.** Completeness, correctness, scope adherence, and conciseness are different questions — set-coverage, claim-verification, reference-grounding, and density/length respectively. A single rubric forces them onto one scale, losing resolution on each.

3. **Rubric-option position bias.** [[am-more-pointwise-pairwise-revealing-position]] shows that rubric-based pointwise scoring is implicitly multi-choice and exhibits position bias over score options. The 0–5 triple is exactly this failure mode, three times.

Because $y^*$ is by construction a validated solution, issue-resolution is already encoded in it — no separate gate is needed; the per-criterion metrics cover it.

## Hypothesis

Three of the four criteria derive from **two multi-premise NLI passes** per section — one in each direction:

- **Agent-side pass**: for each agent fact $a_{s,i}$, run three-way NLI with premise = *all* golden facts in the section ($G_s$) and hypothesis = $a_{s,i}$. Classify the agent fact as `correct` (entail), `wrong` (contradict), or `out-of-scope` (neutral). One NLI call per agent fact.
- **Golden-side pass**: for each golden fact $g_{s,j}$, run NLI with premise = *all* agent facts in the section ($A_s$) and hypothesis = $g_{s,j}$. Classify as `covered` (entail) or `missing` (otherwise). One NLI call per golden fact.

From these labels, with denominators chosen so the metrics are **independent** rather than algebraic duals:

- **Correctness** $= |\text{correct}| / (|\text{correct}| + |\text{wrong}|)$ — among the agent facts that engage with golden (either correctly or incorrectly), what fraction are right? Undefined (reported as N/A) when the agent makes no golden-relevant claims; the scope-adherence metric handles that case.
- **Scope adherence** $= 1 - |\text{out-of-scope}| / |A_s|$ — fraction of agent facts that engage with the reference rather than going off-topic. Measures grounding fidelity, not length.
- **Completeness** $= |\text{covered}| / |G_s|$ — fraction of golden facts that any agent fact entails.

The fourth metric is **not** derived from NLI:

- **Conciseness** (length-based, deterministic) $= f(\text{len}(\hat{y}_s), \text{len}(y^*_s))$ — measures whether the agent says things efficiently within its scope. An agent that repeats the same correct claim five different ways scores full marks on correctness / scope / completeness but fails conciseness. This is the dimension the earlier (matrix-only) sketch silently dropped.

Four metrics, two multi-premise NLI passes per section for three of them, one deterministic length metric for the fourth. Each metric measures a genuinely distinct property — changing one does not mechanically change another. The multi-premise formulation handles granularity mismatch naturally: a coarse agent fact spanning multiple golden atoms is checked against the full golden set as premise; a summary agent section that jointly entails a fine-grained golden fact is checked with all agent facts as premise.

## Approach sketch

**Input assumption.** Golden $y^*$ is **pre-decomposed** into atomic facts per section by the authoring UW. This is reasonable because UW-authored goldens are already structured as bulleted atomic-form propositions (verified in the working example). Formally: for each section $s$, $y^*$ provides $G_s = \{g_{s,1}, \ldots, g_{s,n_s}\}$.

Only $\hat{y}$ requires LLM decomposition.

**Pipeline:**

```
input: (ŷ, y* as pre-decomposed {G_s})
  → section split (deterministic)
  → LLM decomposes ŷ per section → {A_s}
  → for each section s:
        agent-side NLI vector: for each a ∈ A_s, NLI(premise=G_s, hypothesis=a) → {entail, neutral, contradict}
        golden-side NLI vector: for each g ∈ G_s, NLI(premise=A_s, hypothesis=g) → {entail, neutral, contradict}
        length measurement (len(ŷ_s), len(y*_s))
  → derive {correctness_s, scope_s, completeness_s, conciseness_s} per section
  → per-section aggregator → {S_s}
  → global aggregator → S_total
```

**Stage 0 — Section split (deterministic).**
Parse $\hat{y}$ and $y^*$ into sections via the template headers (`Key Highlights`, `Underwriting Insights`, `Underwriting Suggestions`). Regex / markdown parsing; no LLM.

**Stage 1 — Agent decomposition (LLM).**
For each agent section, an LLM decomposer emits atomic facts $A_s = \{a_{s,1}, \ldots, a_{s,m_s}\}$. Golden is already atomic, so no decomposition needed on the reference side.

**Stage 2 — Multi-premise NLI, two passes per section.**

*Agent-side pass (for correctness + scope):*
For each agent fact $a_{s,i}$, a single NLI call with premise = *all* golden facts in the section (joined as a multi-premise) and hypothesis = $a_{s,i}$. Verdict $v^{A}_{s,i} \in \{\text{entail}, \text{neutral}, \text{contradict}\}$.

*Golden-side pass (for completeness):*
For each golden fact $g_{s,j}$, a single NLI call with premise = *all* agent facts in the section and hypothesis = $g_{s,j}$. Verdict $v^{G}_{s,j} \in \{\text{entail}, \text{neutral}, \text{contradict}\}$.

Total: $|A_s| + |G_s|$ NLI calls per section — roughly linear in section size instead of quadratic.

**Stage 3 — Derive metrics.**

*Per agent fact $a_{s,i}$, from $v^{A}_{s,i}$:*

- `contradict` → $a_{s,i}$ is **wrong**
- `entail` → $a_{s,i}$ is **correct**
- `neutral` → $a_{s,i}$ is **out-of-scope**

*Per golden fact $g_{s,j}$, from $v^{G}_{s,j}$:*

- `entail` → $g_{s,j}$ is **covered**
- `neutral` or `contradict` → $g_{s,j}$ is **missing**

(Golden-side `contradict` is rare in practice and redundant with agent-side `wrong`; treating it as `missing` for the completeness metric keeps the definition clean. Any such cases are counted separately on the calibration card as a cross-check that the two passes are consistent.)

*Per-section metrics* (each a fraction in $[0,1]$, higher is better):

$$\text{Correctness}_s = \frac{|\text{correct}|}{|\text{correct}| + |\text{wrong}|} \quad \text{Scope}_s = 1 - \frac{|\text{out-of-scope}|}{|A_s|}$$

$$\text{Completeness}_s = \frac{|\text{covered}|}{|G_s|} \quad \text{Conciseness}_s = f\left(\text{len}(\hat{y}_s), \text{len}(y^*_s)\right)$$

Candidate $f$ for conciseness: $1 - |\text{len}(\hat{y}_s) / \text{len}(y^*_s) - 1|$ clipped to $[0, 1]$, or a UW-calibrated sigmoid on the length ratio. Penalises both over-verbosity and truncation relative to the authored golden.

**Correctness edge case.** When $|\text{correct}| + |\text{wrong}| = 0$ (all agent facts are out-of-scope), correctness is undefined. Report as **N/A** on the calibration card; aggregation treats N/A sections with zero weight on the correctness term and preserves the other three. Scope adherence will be 0 in this case, which is the signal to examine.

The pairwise tie-break problem (one agent fact entailed by some golden, contradicted by others) disappears under multi-premise NLI: each agent fact receives a single verdict from the LLM reasoning over the full golden set as premise. Trade-off: diagnostic traceability — we know an agent fact is `wrong` but not which specific golden fact contradicts it. If per-item traceability is needed for calibration review, a follow-up single-premise NLI pass on just the flagged facts can recover it cheaply.

**Stage 4 — Aggregation.**
Per-section: $S_s = w_\text{comp} \cdot \text{Completeness}_s + w_\text{corr} \cdot \text{Correctness}_s + w_\text{scope} \cdot \text{Scope}_s + w_\text{conc} \cdot \text{Conciseness}_s$. Global: $S_\text{total} = \sum_s \omega_s \cdot S_s$. The four within-section weights $(w_\text{comp}, w_\text{corr}, w_\text{scope}, w_\text{conc})$ and the three cross-section weights $(\omega_\text{highlights}, \omega_\text{insights}, \omega_\text{suggestions})$ are UW-elicited, not uniform. UWs likely weight correctness and completeness much higher than scope-adherence and conciseness.

## Calibration study adaptation

The [[golden-evaluator-calibration-baseline]] methodology shifts from calibrating one holistic judge to calibrating:

- **The agent decomposer** (one LLM component) — per [[factuality-metrics-are-sensitive-to-decomposition-method]], decomposer choice materially affects downstream metrics; measure decomposer quality via [[decomposition-quality-metrics]] or a UW-audited gold decomposition on a small sample.
- **The NLI judge** (per-section, per-pair) — inter-UW κ on individual NLI pairs; per-verdict confusion matrices.

Both are narrower questions than holistic 0–5 rubric judgment — per [[correlation-is-insufficient-for-llm-judge-evaluation]], narrower questions have higher inter-UW agreement ceilings and more interpretable calibration results.

**The calibration card reports per section:**

- Correctness / Scope / Completeness / Conciseness κ and z-score (per [[human-agreement-benchmark-llm-judge]])
- Correctness-undefined rate (sections where all agent facts are out-of-scope)
- Golden-side `contradict` rate (should be near zero under multi-premise NLI; spikes indicate disagreement between the agent-side and golden-side passes worth auditing)
- $|A_s| / |G_s|$ ratios per section (detects systematically off-granularity agent decomposition)
- Agent decomposer quality (decomposer fidelity against UW-audited gold on a small sample)
- NLI confusion matrices (entail / neutral / contradict) vs UW judgments
- Counts of `wrong`, `out-of-scope`, `missing` per section — diagnostic granularity for where agents fail
- Length ratios per section — raw inputs to the conciseness metric

## Expected outcome

- **Four failure modes, named and counted, each measuring a distinct property.** A calibration card gets explicit counts of `wrong` (correctness), `out-of-scope` (scope adherence), `missing` (completeness), and length-ratio extremes (conciseness) rather than blurred 0–5 scores. Failure diagnosis is direct and the four metrics are not algebraic duals — a change in one does not mechanically move another.
- **Compact NLI artefact, auditable.** Two NLI vectors per section (one agent-side, one golden-side). A human reviewer can read them directly; every verdict has a cited hypothesis fact and a three-way label against the full opposite-side premise.
- **Fine-grained PR gradient.** One additional missing fact, one additional contradict, one additional extra are each a visible delta. The holistic rubric would round most such changes away.
- **Sharp inter-UW ceiling per metric.** "Does this agent fact contradict the golden section?" is a much sharper UW question than "is this a 4 or 5 on correctness?", so inter-UW agreement is likely substantially higher — empirically testable, not assumed.

## Future phase: symbolic-solver-aided correctness

The pipeline as specified uses LLM multi-premise NLI as the sole verdict primitive for correctness / scope / completeness. This is the **Phase 1** target — simple to implement, single-primitive, fast to iterate on.

**Phase 2** will replace the NLI judge with a symbolic-solver-aided variant on the subset of agent facts that are rule-application claims (references to UW form codes, mandatory/prohibited-action assertions, exposure classifications). The rationale is developed in the sibling idea [[per-case-smt-routed-correctness-branch]]: LLM NLI can verify surface alignment between an agent claim and the golden section, but cannot verify whether a cited rule's *conditions* are actually satisfied by the case facts. For formalisable claims, a solver provides faithfulness by construction — directly addressing the silent-misapplication failure mode that NLI structurally cannot catch.

The Phase-2 architecture splits the agent-side pass into two branches via a claim classifier:

- **Empirical claims** (e.g. "sources describe the company as commercial") stay with multi-premise NLI against the golden section.
- **Rule-application claims** (e.g. "HS 21 38 is mandatory when operation = residential construction") route to an SMT branch with a per-case KB seeded by golden's cited rules — per [[llm-solver-integration-improves-faithful-reasoning]], [[policy-rules-can-be-formalized-as-smt-constraints]], [[dsl-mediated-autoformalization-improves-translation-robustness]].

Phase 1 is the prerequisite: calibrate the NLI pipeline end-to-end on a UW sample and measure per-section κ against UW judgment. If NLI-on-rule-application claims shows a material gap against UW κ (while NLI-on-empirical claims is in a usable range), Phase 2 is justified. If NLI is uniformly in a usable range, Phase 2 becomes optional — the complexity tax may not pay off.

The sibling idea [[per-case-smt-routed-correctness-branch]] fully specifies the Phase-2 design, including claim classifier, per-case KB construction from golden's rule references, DSL-mediated autoformalization, and minimal-modification-based diagnostics.

## Risks

> [!warning] Risks
> - **Agent decomposer reliability**: [[factuality-metrics-are-sensitive-to-decomposition-method]] shows downstream metrics shift with decomposer choice. Mitigation: UW-audited gold decomposition on a small sample (~10 $\hat{y}$s); pick a fixed decomposer and pin it; document as a calibration-card variable.
> - **NLI reliability on domain text**: general-purpose LLM NLI degrades on specialist language. Mitigation: measure NLI accuracy against UW-annotated verdicts on a sample of (premise-set, hypothesis) pairs; if accuracy is low, consider few-shot prompting with domain examples or a domain-fine-tuned NLI model.
> - **Multi-premise NLI reliability**: standard NLI research is dominated by single-premise corpora; multi-premise at section-scale (5–15 atomic facts as premise) is less battle-tested. The LLM must correctly combine partially-supporting and partially-irrelevant premises to decide the hypothesis's verdict. Mitigation: include multi-premise examples in the NLI prompt's few-shot pool; measure multi-premise NLI accuracy explicitly in calibration, separately from the NLI baseline on single pairs.
> - **Diagnostic traceability loss**: multi-premise NLI flags an agent fact as `wrong` but does not identify which specific golden fact contradicts it. Mitigation: when correctness on a calibration-set sample is audited by UWs, run a targeted single-premise pass on the flagged facts to recover per-item attribution; cost is bounded (only the flagged subset).
> - **Weight elicitation**: $(w_\text{comp}, w_\text{corr}, w_\text{scope}, w_\text{conc})$ and $(\omega_\text{highlights}, \omega_\text{insights}, \omega_\text{suggestions})$ are policy decisions. Mitigation: elicit from UWs via explicit trade-off questions; report sensitivity of rankings to weight perturbations.
> - **Granularity matching (largely addressed by multi-premise)**: when an agent decomposer produces finer or coarser atomic facts than golden's pre-authored ones, multi-premise NLI handles this naturally — the full set of golden facts serves as premise, so a coarse agent claim spanning multiple golden atoms can still be classified. The remaining failure mode is when the agent decomposer is *systematically* off-granularity (e.g. always sub-splitting), inflating `|A_s|` and diluting NLI verdicts. Mitigation: few-shot the decomposer with golden-decomposition examples; measure $|A_s| / |G_s|$ as a calibration-card diagnostic for unexpectedly large ratios.
> - **Compute cost**: $O(|A_s| + |G_s|)$ NLI calls per section (not $\times$); with typical section sizes of 7–10 facts each and 3 sections, expect 45–60 NLI calls per $\hat{y}$. Mitigation: cache golden-side NLI results — the golden-side pass depends only on $y^*$ and the decomposed agent section, so for multiple $\hat{y}$s evaluated against the same $y^*$, golden-side verdicts can be recomputed in O(|G_s|) per $\hat{y}$ but the agent-side pass is unavoidable.

> [!success] Pilot results
> *(not yet run)*

> [!info] Lessons learned
> *(not yet available)*
