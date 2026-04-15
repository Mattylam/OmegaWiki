---
title: "Golden Evaluator calibration baseline (prerequisite)"
slug: golden-evaluator-calibration-baseline
status: proposed
origin: "Reframed purpose of GE: testing instrument for PR optimisation. Before choosing any feedback design (taxonomic / pairwise / distilled), GE itself must be established as a reliable oracle — otherwise all downstream PR-improvement measurements are artifacts of evaluator noise."
origin_gaps: [multi-agent-debate-outperforms-single-agent-judge, bootstrapping-produces-better-demonstrations-than-hand-written]
tags: [golden-evaluator, calibration, benchmark, llm-as-judge, self-learning-phase-2, prerequisite]
domain: NLP
priority: 5
pilot_result: ""
failure_reason: ""
linked_experiments: []
date_proposed: 2026-04-13
date_resolved: ""
---

## Motivation

The Golden Evaluator (GE) serves as a **testing instrument** for Prompt Refiner (PR) optimisation — it is the oracle that tells us whether PR is getting better over iterations, across model choices, across teleprompter configurations, across taxonomy designs. But an instrument is only useful if its readings are reliable. If GE's scores are noisy, biased, or poorly-correlated with underwriter judgment, every "improvement" observed in PR becomes a statistical artifact of evaluator noise rather than real gains. The three feedback-design ideas ([[taxonomic-feedback-prompt-refiner]], [[pairwise-preference-feedback-prompt-refiner]], [[distilled-rationale-feedback-prompt-refiner]]) all assume GE is a trustworthy oracle — this idea questions that assumption and proposes establishing it as a baseline before the others are evaluated.

The issue is surfaced directly by two foundations: [[llm-as-judge]] catalogues sycophancy, position bias, self-preference, and calibration drift as intrinsic LLM-judge failure modes. [[when-ais-judge-ais-rise-agent]] reports empirically that single-LLM judges achieve 0.8–0.9 Spearman correlation with humans in aggregate, but that aggregate figure hides substantial per-task and per-domain variance. For underwriter evaluation specifically (a high-stakes, highly-subjective domain), GE may be materially less reliable than the aggregate literature suggests.

## Hypothesis

Before optimising PR against GE, we can empirically characterise GE's reliability as an oracle by measuring its agreement with underwriter judgment on a held-out set of mock outputs:

1. If Kendall's τ and Spearman's ρ between GE's rankings and UW rankings exceed a threshold (e.g. τ ≥ 0.5, matching the aggregate single-LLM-judge literature), GE is a usable oracle — proceed to the feedback-design ideas
2. If below threshold, no feedback design can rescue the benchmark — GE itself must be improved (multi-agent debate, different base model, role diversification, rubric refinement) before PR optimisation is meaningful
3. Per-criterion agreement may vary: GE may be reliable on completeness but unreliable on conciseness (or vice versa). Per-criterion calibration should be measured and reported independently.

## Approach sketch

**Step 1 — Calibration set construction**
Curate N mock outputs (recommended: 50–100) spanning diverse UW issues $z$, initial prompts $\theta_0$, and account types. Each sample is $(\hat{y}, y^*)$ with the golden output already established.

**Step 2 — Parallel scoring**
- **GE scores**: run the current GE on all N samples. Record per-criterion scores $(s_{\text{comp}}, s_{\text{corr}}, s_{\text{conc}})$ and, if Option B pairwise is a candidate feedback form, pairwise preferences for all $\binom{N}{2}$ comparisons.
- **UW scores**: 2+ UWs independently score the same N samples on the same rubric, blind to GE.

**Step 3 — Agreement metrics**
- Per-criterion rank correlation: [[kendalls-tau]] and [[spearmans-rho]] between GE and each UW, and between UW pairs (inter-UW baseline — you cannot ask GE to beat the inter-UW agreement ceiling)
- Per-criterion score distribution overlap (Bland-Altman-style): does GE systematically over/under-score vs UWs?
- Pairwise agreement rate (if pairwise design is being considered)
- Confusion matrix at the 0–5 ordinal level

**Step 4 — Diagnostic failure modes**
For cases where GE disagrees with UW consensus, classify:
- Sycophancy pattern (did GE prefer verbose/structured output despite being less correct)?
- Adversarial-response indicator (is GE's preferred output gaming specific rubric keywords)?
- Stylistic bias (does GE prefer outputs resembling its own generation style)?

**Step 5 — Pass/fail decision + publication of calibration card**
Publish a "GE calibration card" documenting:
- Per-criterion τ, ρ, and confidence intervals
- Inter-UW agreement ceiling
- Known failure modes catalogued in Step 4
- Sample count and dates
- Whether GE is approved for use as benchmark instrument

Re-run quarterly or after any GE change (prompt, base model, rubric version).

## Expected outcome

- **Calibration card becomes a first-class artifact** that every PR-optimisation experiment must reference. Benchmark results become interpretable because the instrument's reliability is known.
- **If GE fails the calibration bar**, we pivot to GE improvement before PR optimisation — likely candidates: [[multi-agent-debate-outperforms-single-agent-judge]] (Scorer-Critic-Commander pattern), model-family diversification, rubric refinement, or agent-as-a-judge upgrade.
- **If per-criterion reliability diverges substantially** (e.g. GE reliable on correctness but poor on conciseness), the termination predicate `min(S_i) ≥ τ` can be re-weighted to match GE's actual reliability, or conciseness can be assessed by a different mechanism (heuristic length bound, separate judge).
- **Inter-UW agreement ceiling emerges as natural target**: no automated evaluator can exceed UW-vs-UW agreement; this tells us how much room for improvement actually exists.

> [!warning] Risks
> - **UW time cost**: 50–100 samples × 2+ UWs × per-sample scoring is substantial; may require dedicated allocation. Mitigation: bootstrap with a small (N=20) pilot to decide whether full calibration is warranted.
> - **Rubric drift**: if UWs disagree with each other more than expected (low inter-UW agreement), the rubric itself may be ambiguous — the calibration study may reveal the rubric needs sharpening before GE can be judged.
> - **Sampling bias**: calibration set must reflect production distribution; cherry-picking easy or hard cases biases the agreement metrics. Mitigation: stratified sampling across capabilities and UW-issue categories.
> - **Moving target**: UW expectations evolve (per the project's core motivation). A calibration card is a snapshot — requires re-running on a cadence, not a one-time gate.
> - **Calibration ≠ validity**: GE can agree with UWs *and* still be wrong (both can share the same bias, e.g. [[evaluator-collusion]] of a different kind — human-UW-pool collusion with LLM training data). Mitigation: occasionally include adversarial/edge-case probes where the "right" answer is controversial, to test GE's reasoning rather than pattern-matching.
> - **Per-criterion reliability gap**: if GE is reliable on some criteria and not others, naive aggregation over all three hides that. Mitigation: report per-criterion metrics; weight PR-optimisation feedback accordingly.

> [!success] Pilot results
> *(not yet run)*

> [!info] Lessons learned
> *(not yet available)*

## Related ideas

- [[decomposed-reference-based-golden-evaluator]] — structural alternative to the holistic 0–5 rubric that motivates this calibration study. Calibration protocol should be run on both designs for a head-to-head comparison: does decomposition raise the inter-UW ceiling and GE-UW agreement relative to the holistic rubric?
