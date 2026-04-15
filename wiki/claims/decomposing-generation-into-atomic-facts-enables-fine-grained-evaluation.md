---
title: "Decomposing long-form generation into atomic facts enables fine-grained factuality evaluation that can be automated to within 2% of human agreement"
slug: decomposing-generation-into-atomic-facts-enables-fine-grained-evaluation
status: supported
confidence: 0.8
tags: [factuality, atomic-facts, reference-based-evaluation]
domain: NLP
source_papers: [factscore-fine-grained-atomic-evaluation-factual]
evidence:
  - source: factscore-fine-grained-atomic-evaluation-factual
    type: supports
    strength: strong
    detail: "Automated FActScore estimator on long-form biographies achieves less than 2% error vs human-annotated FActScore across 6,500 generations × 13 LLMs. Decomposition into atomic facts + per-fact entailment against Wikipedia evidence recovers a continuous factuality score correlating with human judgment."
conditions: "Validated on biography factuality with Wikipedia as evidence source. The 2% error rate is contingent on (a) a well-behaved decomposer, (b) retrievable evidence in a knowledge source, (c) LLM NLI reliability on the target domain. Not automatically transferable to specialist domains or to reference-based settings where the 'knowledge source' is a single short golden output."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> Long-form generation factuality can be evaluated by decomposing the output into atomic facts and verifying each against a trusted evidence source. Under clean conditions (biography vs Wikipedia), an automated implementation of this pipeline agrees with human-annotated factuality to within 2% error — validating the pipeline shape as a viable foundation for fine-grained reference-based evaluation.

## Evidence summary

- 6,500 generations × 13 LLMs evaluated with the FActScore pipeline
- Automated estimator < 2% error vs human-annotated FActScore on the biography benchmark
- Results reproduce the expected model ordering (GPT-4 > ChatGPT > open models)

> [!info] Conditions and scope
> - Validated on long-form biographies with Wikipedia evidence
> - Requires a decomposer that reliably produces atomic facts (see [[a-closer-look-claim-decomposition]] for the variance issue)
> - Requires retrievable evidence (not guaranteed in domains without a canonical knowledge source)
> - NLI reliability is domain-dependent — the 2% error rate is a ceiling, not a transferable guarantee

> [!warning] Counter-evidence
> - [[a-closer-look-claim-decomposition]] shows FActScore-style metrics are sensitive to the decomposition method used — the 2% error rate assumes the specific FActScore decomposer and may not hold when alternative decomposers are substituted.
> - Domain transfer (specialist language, non-biographical text) has not been systematically evaluated.

## Linked ideas

- [[decomposed-reference-based-golden-evaluator]] — this claim is the foundational existence proof for the pipeline shape; decomposed GE adopts the FActScore-style template (decompose → verify → aggregate) and re-calibrates each component for UW text

> [!question] Open questions
> - Does the < 2% error rate transfer to reference-based settings where evidence is a single short $y^*$ rather than a broad knowledge source?
> - How does NLI reliability degrade on specialist domain text (underwriting, legal, medical)?
