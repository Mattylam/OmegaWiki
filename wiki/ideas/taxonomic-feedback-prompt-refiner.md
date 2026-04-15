---
title: "Taxonomic (closed-vocabulary) feedback for Prompt Refiner"
slug: taxonomic-feedback-prompt-refiner
status: proposed
origin: "Optimisation Phase note — golden-output leakage concern: PR must converge toward θ* without reconstructing y* from feedback"
origin_gaps: [bootstrapping-produces-better-demonstrations-than-hand-written]
tags: [prompt-optimization, golden-evaluator, information-hiding, dspy, self-learning-phase-2]
domain: NLP
priority: 5
pilot_result: ""
failure_reason: ""
linked_experiments: []
date_proposed: 2026-04-13
date_resolved: ""
---

## Motivation

Under the reframed purpose of GE ([[golden-evaluator-calibration-baseline]]) — GE is a **testing instrument** for PR optimisation, not a production quality gate — the feedback design question becomes: what signal makes PR's improvement *measurable* across iterations, runs, and configuration variants?

Closed-vocabulary taxonomic tags give the cleanest benchmark signal for three reasons:
1. **Measurability**: PR behaviour becomes inspectable at the tag level — "did severity-per-criterion decrease over iterations? did PR address the flagged tags?" — rather than being hidden inside free-form text
2. **Test-set integrity**: the vocabulary is bounded by design, so f_i cannot carry y* content; PR cannot "cheat" the benchmark by memorising test labels
3. **DSPy-compilability**: typed tag outputs are directly consumable by `BootstrapFewShot` ([[bootstrap-dspy]]); the benchmark doubles as the training signal

Free-form feedback would carry y* content (test contamination) and also be hard to evaluate across runs (unstructured output is hard to compare). Taxonomic feedback solves both.

## Hypothesis

If GE outputs feedback as structured tags from a pre-defined, UW-informed error taxonomy (closed vocabulary per criterion), then:
1. PR receives sufficient signal to converge, because taxonomic tags map to recurring reasoning-pattern gaps
2. y* cannot be reconstructed from f_i, because the vocabulary size is bounded
3. The signal is directly DSPy-compilable: `FeedbackSignal` becomes a typed output field that `BootstrapFewShot` can learn from ([[bootstrap-dspy]])
4. The priority field emerges naturally from per-criterion severity

## Approach sketch

Define a `FeedbackSignal` schema:

```python
class FeedbackSignal(BaseModel):
    comp_gaps: list[CompletenessTag]   # e.g. MISSING_EXPOSURE_CLASS, MISSING_JURISDICTION_CHECK
    corr_errors: list[CorrectnessTag]  # e.g. OVERSTATED_LIMIT, MISSED_ENDORSEMENT
    conc_issues: list[ConcisenessTag]  # e.g. REDUNDANT_CITATION, VERBOSE_PREAMBLE
    priority: Literal["comp", "corr", "conc"]
    severity_per_criterion: dict[str, int]  # 0–5 each
```

**Taxonomy construction** (one-time, per capability): interview UW experts to enumerate recurring failure modes per criterion. Start with 10–20 tags per criterion; expand as patterns emerge in production.

**GE's job**: classify — given (ŷ, y*), emit the set of applicable tags plus per-criterion severity. Reasoning happens inside GE; only the classification surfaces to PR.

**PR's job**: pattern-match — given the tag set, produce an instruction edit addressing the highest-priority criterion.

## Expected outcome

- PR converges in roughly the same number of iterations as with free-form feedback (no major slowdown, because tag set is rich enough to guide edits)
- Leakage audit (reconstruction attack) shows y* cannot be recovered from f_i better than random baseline
- Taxonomy becomes a self-describing UW ontology that makes the Phase 2 pipeline inspectable and debuggable — a unexpected side benefit
- Phase 2 `BootstrapFewShot` compiles PR from ~50 iteration records once collected

> [!warning] Risks
> - **Taxonomy drift**: UW expectations evolve faster than the taxonomy can be updated; tags become stale. Mitigation: reserve an `OTHER_*` tag per criterion that surfaces "doesn't fit taxonomy" cases for quarterly review.
> - **Leakage through severity**: high severity across many tags may effectively encode y* content (e.g. "10 tags all maxed out" implies specific y* structure). Mitigation: cap tag count per criterion; include leakage audit as a hard gate.
> - **Test-set contamination**: if the taxonomy is derived from the test set itself (tags match test-case specifics too closely), PR will overfit the benchmark. Mitigation: build taxonomy from UW interviews and historical production data, *not* from the test set; reserve the test set for measurement only.
> - **Depends on GE calibration**: this idea assumes GE classifies reliably. If GE miscategorises (wrong tags, wrong severity), PR optimisation measures evaluator noise. [[golden-evaluator-calibration-baseline]] must be run first.
> - **Taxonomy granularity mismatch**: too coarse → GE can't distinguish failure modes; too fine → individual tags encode content. Mitigation: iterate taxonomy resolution based on audit results.
> - **[[adversarial-response]] risk**: PR learns to produce prompts that game the taxonomy (e.g. emit all possible tags) rather than fix the output. Mitigation: hold-out taxonomy-free GE periodically to catch this.
> - **Cross-capability generalisation**: taxonomy designed for `gl_risk_analysis` may not transfer to `nature_of_operations`. May need per-capability taxonomies, which multiplies the upfront cost.

> [!success] Pilot results
> *(not yet run)*

> [!info] Lessons learned
> *(not yet available)*
