---
title: "A Closer Look at Claim Decomposition"
slug: closer-look-claim-decomposition
arxiv: "2403.11903"
venue: "arXiv"
year: 2024
tags: [factuality, atomic-facts, decomposition, reliability, logical-atomism, neo-davidsonian-semantics]
importance: 4
date_added: 2026-04-15
source_type: pdf
s2_id: ""
keywords: [claim decomposition, DecompScore, FActScore sensitivity, neo-Davidsonian semantics, logical atomism]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

[[factscore-fine-grained-atomic-evaluation-factual]] and similar atomic-fact-based factuality metrics attribute the overall factuality score to the generation model under evaluation. But the metric itself has a decomposition step — if the decomposer produces different atomic-fact sets on the same text, the downstream score changes. Prior work did not systematically characterise this sensitivity; consequently, differences in reported FActScores across papers could be artefacts of decomposer choice rather than real differences in generation factuality.

## Key idea

Decomposition quality is a first-class variable. Two contributions: (1) empirically demonstrate that FActScore is sensitive to the decomposition method; (2) propose a principled decomposer grounded in Bertrand Russell's **logical atomism** and **neo-Davidsonian event semantics**, which produces more consistent atomic facts and improves downstream factuality scoring. Introduce **DecompScore** as an adaptation of FActScore that treats decomposition quality as measurable rather than assumed-fixed.

## Method

- Benchmark FActScore under multiple decomposition methods (different prompts, different LLMs) on the same generations.
- Define atomic-fact form using neo-Davidsonian semantics: each atomic fact corresponds to an event with its arguments, producing structurally uniform minimal units.
- Implement a decomposition LLM prompt aligned with this logical form.
- Report DecompScore: a variant of FActScore that benchmarks decomposer performance explicitly.

## Results

- FActScore values **shift materially** across decomposition methods on the same generations — confirming that decomposer choice is a confound rather than a neutral preprocessing step.
- The logical-atomism / neo-Davidsonian decomposer produces atomic facts that are more internally uniform (less variance in atomicity) than single-shot LLM decomposers.
- DecompScore makes decomposer quality an auditable variable, letting future work attribute score changes correctly to generator vs decomposer.

> [!warning] Limitations
> - Neo-Davidsonian logical form is a specific linguistic-theoretic commitment; applicability to non-event-heavy text (e.g. declarative statements about properties, relations, scalar claims) is less directly motivated.
> - Paper is primarily diagnostic — it surfaces the problem and proposes a better decomposer, but does not provide a universal decomposer calibration protocol.
> - Abstract-level content does not quantify the magnitude of the sensitivity effect.

> [!question] Open questions
> - What is a general decomposer-calibration protocol applicable across domains?
> - How do atomic-fact-based metrics interact with claim importance weighting — is a more uniform decomposer automatically better, or can it over-atomise by splitting semantically-bound propositions?
> - Does the decomposer sensitivity finding generalise to reference-based (rather than knowledge-base-retrieval) settings?

> [!tip] My take
> This paper retrospectively repositions every FActScore-style evaluation in the literature as "decomposer-dependent" — a significant epistemic claim. For [[decomposed-reference-based-golden-evaluator]], this is the paper that converts "extractor reliability is a risk" into "extractor calibration is a named pipeline stage with its own metrics". Before running the GE calibration study against UWs, the decomposer must be independently benchmarked — either against UW-annotated atomic points (recall/precision against gold decompositions) or against its own run-to-run consistency. The decomposer is not a fixed preprocessing block; it is a component with bias and variance that propagate through the whole pipeline.

## Related

- [[llm-as-judge]] (foundation — derived_from)
- [[atomic-fact-decomposition]]
- extends: [[factscore-fine-grained-atomic-evaluation-factual]]
- supports: [[factuality-metrics-are-sensitive-to-decomposition-method]]
