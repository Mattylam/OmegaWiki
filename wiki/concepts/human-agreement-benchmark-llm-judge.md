---
title: "Human-Agreement Benchmark for LLM Judges"
aliases: ["Judge's Verdict benchmark", "Turing Test for judges", "human-likeness test for judges", "two-step judge evaluation"]
tags: [llm-as-judge, evaluation-methodology, cohen-kappa, human-agreement]
maturity: active
key_papers: [judges-verdict-comprehensive-analysis-llm-judge]
first_introduced: "2025"
date_updated: 2026-04-15
related_concepts: [llm-judge-bias-taxonomy]
---

## Definition

A methodology for validating an LLM-as-judge against human judgment that progresses beyond rank correlation. It uses (1) correlation as a coarse screen, then (2) Cohen's κ z-scored against the inter-human κ distribution to classify a candidate judge as **human-like** (statistically indistinguishable from a natural human annotator) or **super-consistent** (exceeds human-to-human agreement levels).

> [!tip] Intuition
> Rank correlation is too forgiving. A judge that systematically over-scores everyone by 1 point can have τ = 1.0 while disagreeing with every individual human on the absolute label. Cohen's κ captures per-item agreement; z-scoring against inter-human κ tells you whether the judge is behaving like a human member of the annotation pool or something categorically different (either better or collapsed).
> *Source: LLM analysis*

## Formal notation

For a candidate judge $J$ and a human annotator pool $H = \{h_1, \ldots, h_k\}$:

- Compute pairwise human-human agreements $\kappa(h_i, h_j)$ for all $i < j$; let $\mu_H, \sigma_H$ be the mean and std of this distribution.
- Compute judge-human agreements $\kappa(J, h_i)$ for each $i$.
- Define $z(J) = (\bar{\kappa}(J, H) - \mu_H) / \sigma_H$.
- Classification:
  - $|z(J)| < 1$: **human-like** (natural variation)
  - $z(J) > 1$: **super-consistent** (over-agrees — interpret cautiously)
  - $z(J) < -1$: sub-human (reject)

## Key variants

- **Two-stage protocol**: correlation filter → κ-based Turing test (original formulation)
- **Single-stage κ-only**: skip the correlation filter when the candidate pool is small

## Comparison

| Metric | What it captures | Pitfall |
|---|---|---|
| Spearman ρ / Kendall τ | Rank-order preservation | Insensitive to systematic shifts |
| Cohen's κ | Per-item exact agreement corrected for chance | Sensitive to category imbalance |
| Cohen's κ z-score vs inter-human | Whether judge behaves like a human member | Requires inter-human distribution |

## When to use

Use the two-step benchmark whenever an LLM judge is about to be deployed as a primary evaluation instrument — including a Golden Evaluator for prompt-optimisation pipelines. Report the κ z-score as the headline metric; report correlation as a secondary statistic.

> [!warning] Known limitations
> - Requires a pool of human annotators (≥ 3 for a usable inter-human κ distribution) — sample size matters.
> - "Super-consistent" judges need interpretation: could be over-agreeing with a modal human, could be collapsing legitimate disagreement.
> - Inter-human κ distribution is task-specific and must be re-derived per deployment.
> *Source: LLM analysis*

> [!question] Open problems
> - How to disambiguate "super-consistent = more reliable" from "super-consistent = mode collapse"
> - Extensions to subjective tasks where ground truth itself is contested
> *Source: LLM analysis*

## Key papers

- [[judges-verdict-comprehensive-analysis-llm-judge]] — benchmarks 54 judges; establishes correlation-alone is insufficient; introduces human-like / super-consistent classification

> [!tip] My understanding
> This is the right protocol for [[golden-evaluator-calibration-baseline]] Step 3. Replace the `τ ≥ 0.5` correlation gate with: (a) compute inter-UW κ distribution on the calibration set, (b) compute GE-UW κ and z-score against the distribution, (c) require |z| < 1 for deployment. Also report the full correlation+κ pair so future researchers can diagnose correlation/agreement gaps.
