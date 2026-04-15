---
title: "Judging the Judges: A Systematic Study of Position Bias in LLM-as-a-Judge"
slug: judging-judges-systematic-study-position-bias
arxiv: "2406.07791"
venue: "IJCNLP-AACL"
year: 2024
tags: [llm-as-judge, position-bias, evaluation-reliability, benchmark]
importance: 4
date_added: 2026-04-15
source_type: pdf
s2_id: "dfbfe75ec8c2143e899897a3c054ee58d99ead43"
keywords: [position bias, LLM-as-judge, pairwise comparison, list-wise comparison, repetition stability, position consistency, preference fairness]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

LLM-as-judge is increasingly used in place of human evaluators, but its reliability is compromised by intrinsic biases — chief among them **position bias**, the tendency to favour solutions based on their ordering within the prompt. Prior work acknowledged the phenomenon but lacked a large-scale, multi-judge, multi-task characterisation of when and why it occurs.

## Key idea

Treat position bias as a measurable property of a judge × task pair, quantified by three complementary metrics — **repetition stability**, **position consistency**, **preference fairness** — and empirically decompose its drivers into Judge-Level, Candidate-Level, and Task-Level factors.

## Method

- 15 LLM judges evaluated on MTBench and DevBench (22 tasks, ~40 solution-generating models), yielding >150,000 pairwise and list-wise evaluation instances.
- **Repetition stability**: does the judge return the same verdict when the same prompt is queried repeatedly?
- **Position consistency**: does the judge return the same verdict when the order of candidate solutions is swapped?
- **Preference fairness**: when a position bias exists, is it symmetric (neutral) or skewed toward first/last position?
- Ablations on prompt-component length and inter-candidate quality gap to isolate causal drivers.

## Results

- Position bias is **not random noise**: it varies systematically across judges and tasks and reproduces across repetitions.
- Position bias is **weakly correlated with prompt length** but **strongly correlated with the quality gap** between candidate solutions — when solutions are of similar quality, position bias dominates the verdict.
- Judge agreement/disagreement patterns reveal which dataset items are inherently hard to judge.

> [!warning] Limitations
> - The study is **exploratory and observational** — it characterises bias but does not propose a mitigation technique evaluated against baselines.
> - Restricted to two benchmarks (MTBench, DevBench); whether the findings transfer to domain-specific, high-stakes settings (e.g. underwriter evaluation) is untested.
> - Metrics are defined for pairwise/list-wise settings; pointwise (rubric-based) scoring is addressed only indirectly.

> [!question] Open questions
> - Which mitigations — balanced permutation aggregation, explicit de-biasing prompts, ensembling — most reliably reduce position bias without collapsing discrimination on genuinely different candidates?
> - Does position bias in pairwise settings predict analogous ordering biases in rubric-based scoring?
> - How does position bias interact with the quality-gap ceiling for a given judge-task pair?

> [!tip] My take
> This is the empirical foundation anyone running pairwise-preference feedback must cite. For [[golden-evaluator-calibration-baseline]], the "quality-gap drives bias" finding is directly actionable: when the calibration set contains near-ties, position bias will dominate, inflating observed disagreement even when the judge is otherwise well-calibrated. Report agreement metrics separately for wide-gap vs narrow-gap pairs.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[position-bias-llm-judge]]
- supports: [[llm-judges-exhibit-measurable-position-bias]]
