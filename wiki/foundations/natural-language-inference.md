---
title: "Natural Language Inference"
slug: "natural-language-inference"
domain: "NLP"
status: mainstream
aliases: ["NLI", "textual entailment", "recognising textual entailment", "RTE", "entailment"]
first_introduced: "2005"
date_updated: 2026-04-15
source_url: "https://en.wikipedia.org/wiki/Textual_entailment"
---

## Definition

Natural Language Inference (NLI) is the task of determining the logical relationship between two natural-language sentences: a **premise** $P$ and a **hypothesis** $H$. The canonical formulation is three-way classification: given $(P, H)$, decide whether $P$ **entails** $H$, **contradicts** $H$, or is **neutral** toward $H$ (neither entails nor contradicts). Entailment here is loose rather than logical — it asks whether a typical reader, given $P$, would conclude $H$ is true.

> [!tip] Intuition
> NLI is the minimal primitive for "does this text follow from that text?" It is strictly weaker than formal logical entailment (it admits commonsense and world-knowledge-dependent inferences a theorem prover wouldn't) and strictly stronger than surface similarity (two sentences can be lexically close but not entail each other, or lexically distant but equivalent). In evaluation pipelines, NLI is the workhorse for "does the candidate output agree with the reference?" questions that don't decompose cleanly into SQL-style lookups.
> *Source: LLM analysis*

## Formal notation

$$\text{NLI}: (P, H) \mapsto \{\text{entail}, \text{contradict}, \text{neutral}\}$$

Binary variants collapse `neutral` into either `entail` (loose) or `contradict` (strict); three-way NLI preserves the distinction and is preferred when the cost of false positives and false negatives differs.

Standard evaluation metrics: accuracy on balanced benchmarks (SNLI, MultiNLI, ANLI), F1 per class, and calibration plots for confidence-weighted variants.

## Key variants

- **Pairwise NLI** (the canonical task): single premise, single hypothesis
- **Multi-premise NLI**: multiple premises, one hypothesis — "does this set of facts entail the claim?"
- **Document-level NLI**: premise is a document rather than a sentence; hypothesis is a claim about the document
- **LLM-prompted NLI**: use a general-purpose LLM with a three-way-choice prompt, rather than a dedicated NLI model
- **Specialised NLI models**: fine-tuned on NLI corpora (e.g. DeBERTa-v3-MNLI, models trained on the NLI-Transformers stack)

## Known limitations

- **World-knowledge dependence**: NLI judgments rely on commonsense and background knowledge that a model may lack, especially on specialist-domain text (legal, medical, underwriting)
- **Hypothesis-only bias**: NLI datasets exhibit shortcut features where a classifier can achieve high accuracy using only the hypothesis text, indicating annotator patterns rather than true inference
- **Granularity mismatch**: NLI assumes the hypothesis is a single atomic proposition; long or multi-clause hypotheses produce unstable verdicts because the model has to decide which aspect matters
- **Calibration drift across domains**: general-purpose NLI models degrade materially on specialist domain text without fine-tuning
- **Contradict vs neutral confusion**: strict contradiction ("$P$ asserts $\neg H$") and mere non-support ("$P$ says nothing about $H$") are often conflated by NLI classifiers, especially on short premises

## Open problems

- Reliable NLI on specialist-domain text without full fine-tuning
- Principled handling of document-level premises (do you chunk, embed, or feed the whole document?)
- Aggregating multi-claim NLI into a coherent textual-faithfulness score
- Disentangling the three-way verdict from confidence in settings where downstream aggregation treats `neutral` differently from `contradict`

## Relevance to active research

NLI is the substrate underneath several evaluation patterns tracked in this wiki:

- **Reference-based factuality evaluation** ([[atomic-fact-decomposition]], [[factscore-fine-grained-atomic-evaluation-factual]]): per-atomic-fact NLI against a knowledge source is the verification primitive
- **Decomposed Golden Evaluators** ([[decomposed-reference-based-golden-evaluator]], [[per-case-smt-routed-correctness-branch]]): per-bullet NLI is the backbone of completeness and the empirical-claim branch of correctness
- **LLM-as-judge** ([[llm-as-judge]]): reference-based LLM judging often reduces to LLM-prompted NLI with rubric wrapping
- **Autoformalization validation**: NLI against a back-translated natural-language form of an autoformalization output is one detection method for silent semantic translation errors

The three-way distinction (entail / contradict / neutral) is load-bearing in designs where contradict and neutral map to different downstream actions — e.g. contradict = hard failure, neutral = either hallucination or benign enrichment (reported but not penalised symmetrically).
