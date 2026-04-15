---
title: "Rank correlation alone is insufficient for evaluating LLM judges; per-item agreement (Cohen's κ) relative to the inter-human distribution is required"
slug: correlation-is-insufficient-for-llm-judge-evaluation
status: supported
confidence: 0.8
tags: [llm-as-judge, evaluation-methodology, cohen-kappa, human-agreement]
domain: NLP
source_papers: [judges-verdict-comprehensive-analysis-llm-judge]
evidence:
  - source: judges-verdict-comprehensive-analysis-llm-judge
    type: supports
    strength: strong
    detail: "Across 54 LLM judges, correlation-only filtering admits substantially more candidates than a two-step protocol that adds Cohen's κ z-scored against inter-human agreement. Only 27 of 54 pass the full two-step test."
conditions: "Demonstrated on response-accuracy scoring of RAG / agentic pipeline outputs against ground truth. Transfer to subjective evaluation tasks where ground truth is contested is not yet validated, but the mechanism (correlation ignoring systematic shifts and range-compression) generalises."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> When validating an LLM-as-judge against human judgment, reporting rank correlation (Spearman's ρ or Kendall's τ) alone is insufficient to establish oracle-quality reliability. A judge can achieve near-perfect rank correlation while systematically disagreeing with every individual human on absolute labels. The correct protocol is a two-stage evaluation: (1) correlation as a coarse screen, (2) Cohen's κ z-scored against the inter-human κ distribution to classify the judge as human-like, super-consistent, or sub-human.

## Evidence summary

- 54 LLM judges evaluated on RAG / agentic response-accuracy scoring
- Correlation filter alone admits a large pool; the additional κ-based Turing test restricts Tier 1 to 27 judges
- Judge quality is not a simple function of parameter count — specific training strategies dominate scale

> [!info] Conditions and scope
> - Task: response-accuracy scoring against ground truth
> - Requires a pool of human annotators large enough to estimate the inter-human κ distribution
> - Generalises in principle to any judge-deployment context where per-item agreement matters more than rank ordering

> [!warning] Counter-evidence
> - For coarse-grained leaderboard use cases where only the top-K matter, rank correlation may be sufficient; the κ-based protocol is overkill.
> - "Super-consistent" classification is potentially ambiguous (reliability vs mode collapse) — needs further empirical disambiguation.

## Linked ideas

- [[golden-evaluator-calibration-baseline]] — replace the τ ≥ 0.5 gate with the two-step protocol; report κ z-score as headline metric
- [[decomposed-reference-based-golden-evaluator]] — per-component calibration means the κ-based protocol is run independently on each narrow judge rather than once on a holistic score

> [!question] Open questions
> - Does the two-step protocol retain its discriminative power on subjective tasks without ground truth?
> - How large a human pool is needed to estimate the inter-human κ distribution reliably?
