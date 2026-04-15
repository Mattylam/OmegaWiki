---
title: "Pairwise preference feedback for Prompt Refiner"
slug: pairwise-preference-feedback-prompt-refiner
status: proposed
origin: "Optimisation Phase note — golden-output leakage concern: eliminate the leak by never projecting y* into PR's input"
origin_gaps: [bootstrapping-produces-better-demonstrations-than-hand-written]
tags: [prompt-optimization, golden-evaluator, pairwise-ranking, rlhf, information-hiding, self-learning-phase-2]
domain: NLP
priority: 2
pilot_result: ""
failure_reason: ""
linked_experiments: []
date_proposed: 2026-04-13
date_resolved: ""
---

## Motivation

Under the reframed purpose of GE ([[golden-evaluator-calibration-baseline]]) — GE is a **testing instrument** for PR optimisation — pairwise preference was originally proposed as the cleanest y*-free feedback form. Under this new framing, pairwise ranking has a different and arguably better role: it is the **meta-metric for comparing PR variants**, not the per-iteration feedback signal.

The value of pairwise shifts:
- **As a per-iteration feedback signal** (what this idea originally proposed): too slow, too sparse — 9 bits per iteration makes convergence measurably worse and benchmarks slower
- **As a cross-run evaluation metric** (revised role): comparing two compiled PRs (e.g. `PR_v1` vs `PR_v2`, or `PR_BootstrapFewShot` vs `PR_MIPROv2`) via pairwise win-rate on held-out cases is a well-established, robust evaluation protocol — exactly the approach used in MT-Bench / AlpacaEval / Chatbot Arena ([[when-ais-judge-ais-rise-agent]])

This idea should therefore be **re-scoped**: keep pairwise out of the inner PR-refinement loop, use it at the outer level to decide "did our PR optimisation actually produce a better PR?"

## Hypothesis (revised)

Pairwise preference works best as an **outer-loop comparison metric** rather than an inner-loop training signal:

1. **As a cross-run comparator**: given two PR variants, pairwise win-rate on held-out cases gives a robust, low-variance measure of relative quality — directly comparable to established LLM-eval benchmarks (MT-Bench, AlpacaEval)
2. **As a termination signal inside the loop**: "does θ_i beat θ_{i-1}" is a natural stopping criterion that does not require re-computing full scores each iteration
3. **As GE calibration validation**: pairwise preferences are the simplest GE output to benchmark against UW preferences via [[kendalls-tau]] — this is part of [[golden-evaluator-calibration-baseline]]

The original hypothesis (pairwise as the sole per-iteration PR feedback) is downgraded: the 9-bit signal is too sparse to drive fast convergence inside the refinement loop when taxonomic feedback (Option A) offers richer signal with equivalent test-set integrity.

## Approach sketch

**New loop shape**:

```
ŷ_i = f(x; θ_i + g)
f_i = GE_pairwise(ŷ_i, ŷ_{i-1})  →  {comp: ⬆|⬇|=, corr: ⬆|⬇|=, conc: ⬆|⬇|=}
```

GE still has access to y* internally (to ground its judgment), but only emits the trinary per-criterion preference. No severity, no tags, no content.

**PR's job**: gradient descent interpretation — given the sign of change per criterion, decide whether to (a) extend the last edit direction, (b) reverse it, or (c) pivot to a different criterion. This is a natural fit for a DSPy `Predict` module with signature `(theta_prev, last_edit, preference_signal) -> theta_new`.

**Termination**: redefine readiness as "θ_i beats ŷ_0 on all criteria" (absolute improvement from baseline) rather than "min(S) ≥ τ". Requires anchoring against initial output rather than against y*.

## Expected outcome

- **Zero leakage by construction** — no reconstruction attack can succeed because f_i contains at most 9 possible states
- 2–3× slower convergence than Option A (typical for preference-based vs target-based optimisation — fundamental trade-off)
- Cleaner theoretical framing: the loop becomes a zero-order optimisation over a preference oracle, with convergence guarantees from the pairwise-comparison optimisation literature
- Directly consumable by RLHF-style methods in the future if you decide to fine-tune rather than prompt-optimise ([[fine-tuning]])

> [!warning] Risks
> - **Slow convergence**: 2–3× more iterations to reach readiness; at scale this is expensive (more LLM calls). Mitigation: combine with Option A taxonomy as tie-breaker once signal saturates.
> - **Role confusion**: using pairwise as both the inner-loop signal AND the outer-loop benchmark creates a validity problem (the optimiser is being scored by its own training signal). Mitigation: under the revised framing, reserve pairwise for outer-loop comparison only; use Option A for inner-loop feedback.
> - **Pairwise noise**: GE may be inconsistent on close calls ("is this marginally better?"); noisy preference signal stalls PR. Mitigation: require confidence threshold per preference; "tie" when uncertain.
> - **Anchor drift**: comparing ŷ_i against ŷ_{i-1} can drift — a small improvement from a bad baseline can look like success indefinitely. Mitigation: also track ŷ_i against ŷ_0 as a global anchor every K iterations.
> - **No priority signal**: PR can't tell which criterion to target next without severity. Mitigation: add a meta-preference "which criterion most affects overall quality" as a separate signal (adds 3 more bits per iteration — still information-theoretically clean).
> - **Termination ambiguity**: without absolute scores, when do you stop? Requires a separate "absolute quality check" oracle that only fires at termination — introduces a new potential leak point.

> [!success] Pilot results
> *(not yet run)*

> [!info] Lessons learned
> *(not yet available)*
