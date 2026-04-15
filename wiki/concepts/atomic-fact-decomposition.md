---
title: "Atomic Fact Decomposition"
aliases: ["atomic facts", "atomic claims", "fact decomposition", "claim atomization", "atomic proposition extraction", "claim decomposition"]
tags: [factuality, reference-based-evaluation, decomposition, nli]
maturity: active
key_papers: [factscore-fine-grained-atomic-evaluation-factual, closer-look-claim-decomposition, decmetrics-structured-claim-decomposition-scoring-factually]
first_introduced: "2023"
date_updated: 2026-04-15
related_concepts: []
---

## Definition

Atomic fact decomposition is the operation of breaking a long-form text into a set of minimal, self-contained, independently-verifiable factual propositions ("atomic facts" or "atomic claims"). Each atomic fact is small enough that its truth-value against a reference or knowledge source can be decided by a single entailment judgment, enabling per-claim verification and fine-grained aggregation of factuality scores.

> [!tip] Intuition
> A paragraph saying "Marie Curie, born in Warsaw in 1867, won the Nobel Prize in Physics in 1903 for her work on radioactivity" contains four atomic facts: (a) Marie Curie was born in Warsaw, (b) Marie Curie was born in 1867, (c) Marie Curie won the Nobel Prize in Physics, (d) she won in 1903 for radioactivity. Verifying the paragraph holistically forces a coarse correct/incorrect judgment; verifying the four atomic facts independently localises where the text is right or wrong.
> *Source: LLM analysis*

## Formal notation

Given a text $y$, decomposition is a function $\text{dec}: y \mapsto \{a_1, \ldots, a_n\}$ where each $a_i$ is an atomic fact. Factuality evaluation then reduces to:

$$\text{FactScore}(y \mid E) = \frac{1}{n} \sum_{i=1}^{n} \mathbb{1}[E \models a_i]$$

where $E$ is the evidence source (knowledge base, golden output, or document) and $\models$ is an entailment judgment (typically LLM-based NLI).

## Key variants

- **Single-shot LLM decomposition** (FActScore): prompt an LLM to list atomic facts from the text.
- **Russellian logical-atomism decomposition** (DecompScore, see [[a-closer-look-claim-decomposition]]): decomposition grounded in event-semantic / Davidsonian logical forms for improved consistency.
- **Molecular clauses + atomic facts** (ARE framework): two-stage — sentence → molecular clauses → atomic facts.
- **Iterative adaptive decomposition** (AFEV): dynamic re-decomposition when validation evidence is ambiguous.
- **Sentence-level approximation**: treat each sentence as one atomic fact — coarser but cheaper.

## Comparison

| Decomposition style | Granularity | Compute cost | Decomposition variance |
|---|---|---|---|
| Sentence-level | Coarse | Low | Low (deterministic) |
| Single-shot LLM | Medium | Medium | High (LLM-specific) |
| Logical-atomism (DecompScore) | Fine | High | Lower than single-shot |
| Iterative (AFEV) | Adaptive | Highest | Depends on convergence |

## When to use

- Fact-checking and factuality metrics for long-form generation
- Reference-based evaluation with a trusted source (knowledge base or reference text)
- Per-criterion judge components in structured evaluation pipelines (e.g. [[decomposed-reference-based-golden-evaluator]])
- Any setting where a holistic correct/incorrect verdict collapses meaningful gradient

> [!warning] Known limitations
> - **Decomposition variance**: different decomposers produce different atomic-fact sets on the same text — and [[a-closer-look-claim-decomposition]] shows this materially affects downstream factuality scores.
> - **Granularity ambiguity**: "atomic" is not formally defined; in practice, decomposition LLMs over- or under-split depending on prompt and domain.
> - **Semantic drift**: a decomposer may subtly alter meaning during extraction (e.g. dropping hedges, universalising specific claims) — atomic facts may be factual but misrepresent the original.
> - **Equal-weight assumption**: naive FActScore treats all atomic facts equally. Importance-weighted variants (e.g. VITAL) exist but are not universally adopted.
> *Source: LLM analysis*

> [!question] Open problems
> - Defining "atomic" formally and training decomposers to that target
> - Measuring and controlling decomposition variance across models
> - Distinguishing claims that are *unrelated* to the evidence (benign) from claims that are *contradicted* (hallucinations)
> - Importance weighting: which atomic facts matter for the downstream judgment and which do not
> *Source: LLM analysis*

## Key papers

- [[factscore-fine-grained-atomic-evaluation-factual]] — canonical introduction; four-step pipeline, biography benchmark, <2% automated-vs-human error rate
- [[closer-look-claim-decomposition]] — demonstrates FActScore sensitivity to decomposer choice; introduces DecompScore and logical-atomism / neo-Davidsonian decomposer
- [[decmetrics-structured-claim-decomposition-scoring-factually]] — automated three-metric framework (completeness / correctness / semantic entropy) for decomposer calibration; see [[decomposition-quality-metrics]]

> [!tip] My understanding
> For [[decomposed-reference-based-golden-evaluator]], this concept is the foundation for both the completeness and correctness components. The decomposer is a first-class system component that must be calibrated (for recall against UW-annotated atomic points, for decomposition-variance across runs) before the GE calibration study is meaningful. Downstream literature ([[a-closer-look-claim-decomposition]], [[decmetrics-structured-claim-decomposition]]) provides both better decomposers and tools to measure decomposition quality.
