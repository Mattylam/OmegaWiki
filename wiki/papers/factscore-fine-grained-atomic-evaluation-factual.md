---
title: "FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation"
slug: factscore-fine-grained-atomic-evaluation-factual
arxiv: "2305.14251"
venue: "EMNLP"
year: 2023
tags: [factuality, atomic-facts, reference-based-evaluation, long-form-generation, factual-precision]
importance: 5
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [atomic facts, factual precision, long-form generation, reference-based evaluation, factuality metric]
domain: NLP
code_url: "https://github.com/shmsw25/FActScore"
cited_by: []
---

## Problem

Long-form LLM generations mix supported, unsupported, and partially-supported information. Binary "is this output correct?" judgments collapse that mixture into noise. Human evaluation at sentence / claim granularity is expensive and slow. An automated, fine-grained factuality metric is needed — one that decomposes a generation into independently-verifiable units and scores coverage against a trusted source.

## Key idea

Evaluate factuality at the **atomic-fact** level: decompose a long-form generation into minimal, self-contained factual propositions, verify each against a knowledge source, and report **factual precision** as the fraction of atomic facts that receive evidence support. Human-grade evaluation becomes a structural property of the decomposition + verification pipeline rather than a holistic judgment.

## Method

Four-step pipeline:

1. **Atomic fact generation** — break the generation into minimal independently-verifiable propositions (typically one fact per short sentence).
2. **Evidence retrieval** — for each atomic fact, retrieve candidate evidence from a trusted knowledge source (Wikipedia in the paper's setup).
3. **Fact validation** — an LLM judges whether retrieved evidence entails the atomic fact.
4. **Score computation** — FActScore = (# supported atomic facts) / (# total atomic facts). Reported per-generation.

Both a human-annotated benchmark (biographies of people, factuality vs Wikipedia) and an automated estimator are released.

## Results

- Automated FActScore estimator achieves **< 2% error rate** vs human-annotated FActScore on the biography benchmark.
- Evaluation across 13 LLMs × 6,500 generations: **ChatGPT scores ~58%** on biography factuality — substantial unsupported content in long-form generation.
- GPT-4 > ChatGPT > public open models; Vicuna and Alpaca are the strongest open-source models at the time of release.

> [!warning] Limitations
> - Benchmark is **biographies with Wikipedia evidence** — a setting with clean, structured ground truth. Transfer to domains without a canonical knowledge source (e.g. specialist evaluation against a small reference) is not directly validated.
> - Pipeline is **sequential and additive**: decomposition errors propagate into retrieval and validation; the <2% error rate assumes a well-functioning decomposition step, which [[a-closer-look-claim-decomposition]] later shows is not universally achievable.
> - Atomic-fact granularity is not explicitly defined — what counts as "one fact" is implicit in the decomposition LLM's behaviour. Different decomposers produce different fact sets.
> - Score treats all atomic facts equally; importance-weighting (central vs peripheral facts) is not addressed.

> [!question] Open questions
> - How does FActScore transfer to reference-based settings where the trusted source is a specific golden output $y^*$ rather than a general knowledge base?
> - What is the right aggregation when a claim is neither entailed nor contradicted but unrelated to the source (the three-way NLI case)?
> - How stable is the score under decomposition variation — same generation, different decomposers?

> [!tip] My take
> This is the pipeline template for [[decomposed-reference-based-golden-evaluator]]. The setup maps almost directly: replace "Wikipedia" with "$y^* \cup z$", replace "biography" with "UW output". Two caveats matter for the transfer:
> (1) the <2% error rate is setup-specific and must be re-measured on UW text — specialist domains likely degrade NLI reliability;
> (2) the paper does not confront decomposition-variance, which the subsequent literature ([[a-closer-look-claim-decomposition]], [[decmetrics-structured-claim-decomposition]]) shows is a first-class concern. The decomposed GE cannot naively inherit FActScore's error rate — it inherits the *pipeline shape*, and must re-calibrate each component.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[atomic-fact-decomposition]]
- supports: [[decomposing-generation-into-atomic-facts-enables-fine-grained-evaluation]]
