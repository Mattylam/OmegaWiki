---
title: "Decomposer quality can be measured automatically via completeness, correctness, and semantic-entropy metrics, enabling decomposer benchmarking and fine-tuning without human annotation"
slug: decomposer-quality-can-be-measured-automatically
status: weakly_supported
confidence: 0.65
tags: [factuality, atomic-facts, decomposition, evaluation-framework, decomposer-calibration]
domain: NLP
source_papers: [decmetrics-structured-claim-decomposition-scoring-factually]
evidence:
  - source: decmetrics-structured-claim-decomposition-scoring-factually
    type: supports
    strength: moderate
    detail: "DecMetrics introduces three automatic metrics (COMPLETENESS, CORRECTNESS, SEMANTIC ENTROPY) for scoring claim decomposition quality, and demonstrates the metrics can serve as a training signal for an optimised lightweight decomposer. The framework enables decomposer benchmarking without human annotation."
conditions: "Confidence is `weakly_supported` pending independent replication and pending evidence that DecMetrics scores correlate with downstream factuality-score accuracy. The framework is automated and inherits LLM-judging-LLM failure modes; human-audited sanity checks remain important."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> The quality of a claim-decomposition system can be evaluated automatically using a three-metric framework: completeness (coverage of source information), correctness (faithfulness of atomic claims to source), and semantic entropy (consistency across runs). These metrics enable head-to-head comparison of decomposers and provide a training signal for decomposer fine-tuning, converting decomposer selection from an ad hoc choice into a measured engineering decision.

## Evidence summary

- Three-metric framework (COMPLETENESS, CORRECTNESS, SEMANTIC ENTROPY) defined and operationalised
- Used to optimise a lightweight decomposer competitive with larger LLM decomposers
- Provides a reproducible benchmark target for the claim-decomposition subfield

> [!info] Conditions and scope
> - Demonstrated on general claim decomposition; domain transfer not systematically evaluated
> - Correlation between DecMetrics scores and downstream factuality-metric reliability is plausible but not yet quantified from the abstract
> - The framework is new (Sept 2025); replication is pending

> [!warning] Counter-evidence
> - Not yet catalogued. Potential counter-evidence: a decomposer with high DecMetrics scores that produces unstable downstream FActScore values, or a decomposer that fails DecMetrics but yields reliable end-to-end evaluation.

## Linked ideas

- [[decomposed-reference-based-golden-evaluator]] — DecMetrics becomes the extractor-calibration toolkit: atomic-point and atomic-claim extractors gated by DecMetrics thresholds before GE calibration against UWs
- [[golden-evaluator-calibration-baseline]] — per-component calibration card should include DecMetrics scores for extractor components alongside GE-UW agreement metrics

> [!question] Open questions
> - Does optimising a decomposer against DecMetrics yield measurable improvements in downstream factuality-metric reliability?
> - How do the three DecMetrics dimensions trade off in practice? Is there a Pareto frontier?
