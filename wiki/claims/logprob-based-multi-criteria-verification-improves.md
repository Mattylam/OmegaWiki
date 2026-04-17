---
title: "Logprob-based multi-criteria verification improves trajectory selection over discrete LLM-as-a-Judge scoring"
slug: logprob-based-multi-criteria-verification-improves
status: weakly_supported
confidence: 0.55
tags: [llm-evaluation, verification, reward-model, test-time-scaling, criteria-decomposition, logprob-scoring]
domain: NLP
source_papers: [llm-verifier-general-purpose-verification-framework]
evidence:
  - source: llm-verifier-general-purpose-verification-framework
    type: supports
    strength: moderate
    detail: "LLM-as-a-Verifier achieves 86.4% on Terminal-Bench 2 (vs 81.8% Pass@1) and 77.8% on SWE-Bench Verified (vs 76.1% Pass@1) using logprob distributions over fine-grained score tokens with repeated multi-criteria verification"
conditions: "Tested only with Gemini as verifier model on coding agent benchmarks (Terminal-Bench 2, SWE-Bench Verified); requires logprob access from the verifier model"
date_proposed: 2026-04-16
date_updated: 2026-04-16
---

> [!abstract] Statement
> Using the full logprob distribution over fine-grained score tokens -- scaled along three orthogonal axes (scoring granularity G, repeated verification K, criteria decomposition C) -- produces more reliable trajectory rewards than discrete single-score LLM-as-a-Judge evaluation, enabling improved best-of-N selection in test-time scaling.

## Evidence summary

| Source | Type | Strength | Detail |
|---|---|---|---|
| [[llm-verifier-general-purpose-verification-framework]] | supports | moderate | +4.6% on Terminal-Bench 2 and +1.7% on SWE-Bench Verified over Pass@1 baselines using G=20, K=4, C=3 |

> [!info] Conditions and scope
> - Validated only on coding/software engineering benchmarks (Terminal-Bench 2, SWE-Bench Verified)
> - Verifier model is Gemini via Vertex AI; transferability to other verifier models is untested
> - Requires logprob access (not all LLM APIs expose this)
> - Round-robin tournament selection adds $O(N^2)$ cost over simpler selection strategies
> - No ablation study published separating contributions of G, K, and C individually

> [!warning] Counter-evidence
> No direct counter-evidence identified. However, the lack of formal ablations means the contribution of each axis (G, K, C) is not individually validated -- it is possible that one axis dominates and the others add marginal value.

## Linked ideas

> [!question] Open questions
> - Does the method generalize beyond coding benchmarks to open-ended generation or reasoning tasks?
> - What is the optimal allocation of compute budget across G, K, and C?
> - How does verifier model strength interact with the framework's effectiveness?
