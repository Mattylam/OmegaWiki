---
title: "DecMetrics: Structured Claim Decomposition Scoring for Factually Consistent LLM Outputs"
slug: decmetrics-structured-claim-decomposition-scoring-factually
arxiv: "2509.04483"
venue: "arXiv"
year: 2025
tags: [factuality, atomic-facts, decomposition, evaluation-framework, decomposer-calibration]
importance: 3
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [claim decomposition, DecMetrics, completeness, correctness, semantic entropy, decomposer evaluation]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

[[closer-look-claim-decomposition]] established that atomic-fact-based factuality metrics are sensitive to decomposer choice — yet practitioners had no automated framework to measure decomposition quality itself. Human annotation of "is this a good decomposition?" is slow and expensive; without an automated metric, decomposer calibration remains ad hoc.

## Key idea

Introduce **DecMetrics** — a framework of three automatic metrics that jointly score decomposition quality without human labels. Each metric targets a distinct failure mode: missing content (completeness), hallucinated or mutated claims (correctness), and inconsistent decomposition across runs (semantic entropy). Together they enable decomposer benchmarking, selection, and fine-tuning.

## Method

Three component metrics:

1. **COMPLETENESS** — does the atomic-claim set cover the information in the original text? Measures whether claims in the source text are represented in the decomposition.
2. **CORRECTNESS** — are the atomic claims themselves factually faithful to the source? Penalises claims the decomposer has hallucinated, mutated, or over-generalised.
3. **SEMANTIC ENTROPY** — measures consistency of decompositions across multiple runs on the same text. High entropy ⇒ the decomposer is non-deterministic in semantically meaningful ways.

The framework is used both to benchmark existing decomposers and to **optimise a lightweight decomposition model** — i.e. DecMetrics as a training signal for improved decomposers.

## Results

- Framework produces comparable scores across decomposers, enabling head-to-head comparison
- A lightweight model optimised against DecMetrics is claimed to match or exceed larger LLM decomposers on quality
- Sets a reproducible benchmark target for claim-decomposition research

> [!warning] Limitations
> - Abstract-level content does not quantify the three metrics' correlation with downstream factuality-score accuracy — i.e. does optimising DecMetrics translate to lower FActScore error?
> - Framework defines metrics; definition of "atomic" granularity remains implicit.
> - Evaluation coverage (domains tested, decomposers benchmarked) not detailed from the abstract.

> [!question] Open questions
> - Do DecMetrics scores predict downstream metric stability (e.g. would a high-completeness, low-entropy decomposer produce more reproducible FActScore values)?
> - How do the three metrics trade off — a decomposer maximising completeness might decrease correctness (over-splitting creates malformed claims); what's the Pareto frontier?
> - Does DecMetrics generalise to specialist domain text where "completeness" requires domain knowledge to judge?

> [!tip] My take
> This is the practical tool the extractor-calibration step in [[decomposed-reference-based-golden-evaluator]] needs. Before running GE calibration against UWs, benchmark the atomic-point extractor ($y^* \to$ points) and atomic-claim extractor ($\hat{y} \to$ claims) with DecMetrics. Fail any extractor that falls below a threshold on completeness, correctness, or semantic entropy before investing UW time on GE-agreement measurement. One caveat: DecMetrics is automated and so inherits the same "LLM-judging-LLM" concerns as the rest of the stack; sanity-check the extractor at least on a small UW-audited gold decomposition set.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[atomic-fact-decomposition]]
- [[decomposition-quality-metrics]]
- extends: [[closer-look-claim-decomposition]]
- extends: [[factscore-fine-grained-atomic-evaluation-factual]]
- supports: [[decomposer-quality-can-be-measured-automatically]]
