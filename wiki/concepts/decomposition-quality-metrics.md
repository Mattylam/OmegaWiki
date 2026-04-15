---
title: "Decomposition Quality Metrics (DecMetrics)"
aliases: ["DecMetrics", "decomposer calibration metrics", "decomposition quality scoring", "atomic-claim quality metrics"]
tags: [factuality, atomic-facts, decomposition, evaluation-framework, decomposer-calibration]
maturity: emerging
key_papers: [decmetrics-structured-claim-decomposition-scoring-factually]
first_introduced: "2025"
date_updated: 2026-04-15
related_concepts: [atomic-fact-decomposition]
---

## Definition

An automated framework of three complementary metrics that jointly score the quality of a claim-decomposition system, without human annotation. The framework enables head-to-head comparison of decomposers and provides a training signal for fine-tuning lightweight decomposer models.

> [!tip] Intuition
> If your factuality pipeline routes every claim through a decomposer, and the decomposer is unreliable, the whole pipeline reports garbage. DecMetrics is a dashboard for the decomposer — three dials you can read to decide if it's trustworthy enough to deploy. The dials measure: *does it cover everything* (completeness), *are the pieces it produces faithful* (correctness), *does it produce the same decomposition twice* (semantic entropy).
> *Source: LLM analysis*

## Formal notation

For a decomposer $D$ and a text $y$ with decomposition $D(y) = \{a_1, \ldots, a_n\}$:

- **Completeness**: what fraction of the information in $y$ is covered by $\{a_i\}$?
- **Correctness**: what fraction of $\{a_i\}$ is faithful (entailed by and non-mutative of) $y$?
- **Semantic entropy**: $H(D(y))$ over multiple independent runs — how much does the decomposition vary in semantic content (not just surface form)?

A high-quality decomposer has high completeness, high correctness, and low semantic entropy.

## Key variants

- **DecMetrics (original)**: the three-metric triple above
- **DecompScore** (see [[closer-look-claim-decomposition]]): an earlier single-metric alternative focused on decomposer quality as an adaptation of FActScore
- **Downstream-only measurement**: calibrate decomposers by running the full factuality pipeline and measuring stability of the final score — less principled but requires no new infrastructure

## Comparison

| Metric | Failure mode targeted | Requires human labels |
|---|---|---|
| DecMetrics Completeness | Missing content | No |
| DecMetrics Correctness | Hallucinated / mutated claims | No |
| DecMetrics Semantic Entropy | Run-to-run inconsistency | No |
| DecompScore | Overall decomposer quality | No |
| FActScore stability (downstream) | End-to-end score variance | No but implicit |

## When to use

- Before deploying any atomic-fact-based evaluation pipeline ([[decomposed-reference-based-golden-evaluator]], FActScore-style metrics) — audit the decomposer component with DecMetrics.
- When selecting among candidate decomposers (open-weight LLM vs GPT-4 vs specialised neo-Davidsonian decomposer).
- As a training signal for decomposer fine-tuning.

> [!warning] Known limitations
> - Automated — inherits "LLM-judging-LLM" failure modes, so a DecMetrics-passing decomposer is not guaranteed to match human-annotated atomic-fact gold.
> - The three metrics may trade off: a decomposer maximising completeness can violate correctness by over-splitting; the Pareto frontier is not characterised.
> - Not yet validated across domains — specialist text (underwriting, legal, medical) may require domain-specific variants.
> *Source: LLM analysis*

> [!question] Open problems
> - Predictive validity: does a decomposer with better DecMetrics yield measurably more stable downstream factuality scores?
> - Domain transfer: how to extend DecMetrics to specialist domains where completeness requires domain expertise to judge
> - Integration with importance weighting: DecMetrics treats all claims equally, but a decomposer that reliably captures *important* claims (and misses trivial ones) may be preferable in practice
> *Source: LLM analysis*

## Key papers

- [[decmetrics-structured-claim-decomposition-scoring-factually]] — introduces DecMetrics and a lightweight decomposer optimised against it

> [!tip] My understanding
> For [[decomposed-reference-based-golden-evaluator]], DecMetrics provides the calibration-prerequisite toolkit. Before running the GE calibration study, the atomic-point extractor (for completeness) and atomic-claim extractor (for correctness) should each be scored on DecMetrics. Set thresholds — e.g. completeness ≥ 0.9, correctness ≥ 0.95, semantic entropy below a benchmark — and fail-fast on extractors below the bar. This converts "extractor reliability is a risk" into a gated pipeline stage with pass/fail criteria.
