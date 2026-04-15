---
title: "LLM judges exhibit systematic biases across 12 distinct categories, with robustness varying substantially across models and bias types"
slug: llm-judges-exhibit-systematic-multi-category-biases
status: supported
confidence: 0.75
tags: [llm-as-judge, bias, taxonomy, evaluation-framework]
domain: NLP
source_papers: [justice-prejudice-quantifying-biases-llm-judge]
evidence:
  - source: justice-prejudice-quantifying-biases-llm-judge
    type: supports
    strength: moderate
    detail: "CALM framework evaluated 6 LLM judges across 12 synthetic bias categories. Robustness varies by model (Claude-3.5 ~0.985 on alignment; GPT-4o ~0.977 on facts) and by bias category (Fallacy-Oversight is consistently weak across all tested judges)."
conditions: "Measured on synthetic probes designed to isolate each bias. Real-world prevalence of each bias, and interactions among biases, are not directly characterised. General-purpose benchmarks; transfer to domain-specific high-stakes judgment is untested."
date_proposed: 2026-04-15
date_updated: 2026-04-15
---

> [!abstract] Statement
> LLM-as-judge systems are vulnerable to a broad, structured family of biases — at least 12 distinct categories including Position, Verbosity, Compassion-Fade, Bandwagon, Distraction, Fallacy-Oversight, Authority, Sentiment, Chain-of-Thought, Self-Enhancement, Refinement-Aware, and Diversity. Robustness scores are model-dependent and category-dependent; Fallacy-Oversight emerges as a consistent weak spot across current judges.

## Evidence summary

- 12-category taxonomy evaluated on 6 popular LLM judges via the CALM framework
- Robustness varies within and across models; no judge is uniformly robust
- Fallacy-Oversight is the category where all tested judges underperform

> [!info] Conditions and scope
> - Probes are synthetic; CALM measures robustness-on-probes rather than real-world-bias-prevalence
> - Inter-bias interactions are not modelled
> - Tested judges are general-purpose — domain-specialist judges may have different profiles

> [!warning] Counter-evidence
> - Not yet catalogued. Possible counter-evidence: a judge that passes CALM but fails human-calibration (indicating CALM coverage gaps), or vice versa.

## Linked ideas

- [[golden-evaluator-calibration-baseline]] — adopt CALM's 12-category schema for Step 4 failure-mode classification
- [[decomposed-reference-based-golden-evaluator]] — alternative GE design that shrinks the per-judgment bias surface by replacing holistic rubric scoring with narrow per-claim NLI and deterministic length metrics

> [!question] Open questions
> - Do CALM probe scores predict real-world disagreement with human experts on a held-out calibration set?
> - Is the 12-category taxonomy reducible to a smaller latent structure?
