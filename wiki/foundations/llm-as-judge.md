---
title: "LLM-as-Judge"
slug: "llm-as-judge"
domain: "NLP"
status: mainstream
aliases: ["LLM evaluator", "AI judge", "agent-as-a-judge", "LLM-based evaluation"]
first_introduced: "2023"
date_updated: 2026-04-13
source_url: ""
---

## Definition

LLM-as-judge is an evaluation paradigm where a language model assesses the quality of outputs produced by another model or system, substituting for or supplementing human evaluation. The judge model receives the output (and optionally the input, reference answer, or rubric) and produces a quality score, ranking, or structured critique.

> [!tip] Intuition
> Human evaluation is expensive, slow, and does not scale. LLM-as-judge uses a capable model to approximate human judgment — reading an output and deciding whether it is correct, complete, relevant, or well-formatted. The key design question is how to make the judge reliable: avoiding sycophancy, ensuring calibration, and detecting when the judge is wrong.
> *Source: LLM analysis*

## Formal notation

Given an input $x$, a candidate output $y$, and optionally a reference $y^*$, the judge model $J$ produces an evaluation:

$$e = J(x, y, y^*, \text{rubric})$$

where $e$ may be a scalar score, a boolean verdict, or a structured assessment. Quality is measured by agreement with human judgments, typically via Kendall's $\tau$ or Cohen's $\kappa$.

## Key variants

- **Pointwise evaluation**: judge scores a single output independently
- **Pairwise comparison**: judge compares two outputs and selects the better one
- **Reference-based**: judge compares output against a gold-standard reference
- **Reference-free**: judge assesses output quality without a reference
- **Rubric-based**: judge follows an explicit scoring rubric with criteria and scales
- **Multi-agent debate**: multiple judge agents argue and converge on a verdict
- **Agent-as-a-judge**: agentic judges that can use tools, retrieve context, and reason over multi-step outputs

> [!warning] Known limitations
> - **Sycophancy**: judges may favor verbose, confident, or agreeable outputs over correct ones (see [[sycophancy]])
> - **Position bias**: in pairwise comparison, judges may prefer the first or second output
> - **Self-preference bias**: models tend to rate their own outputs higher
> - **Collusion risk**: generator and judge may converge on shared failure modes when both are LLMs
> - **Calibration drift**: judge reliability varies across domains and difficulty levels
> *Source: LLM analysis*

> [!question] Open problems
> - Ensuring judge independence when generator and judge share similar training data
> - Detecting and mitigating adversarial responses crafted to game the judge
> - Calibrating confidence — knowing when the judge's verdict is unreliable
> - Scaling granular, step-level evaluation for multi-step agentic outputs
> *Source: LLM analysis*

> [!info] Relevance to active research
> LLM-as-judge is the core evaluation pattern used throughout the self-learning pipeline. The Output Evaluator uses it for feedback-resolution verification and prompt-alignment checking. The Regression Check uses it to detect unintended output changes across accounts. The Classifier's three steps each use an LLM as a judge on specific questions (is feedback addressed? do instructions cover this? is data present?). Ensuring judge reliability — avoiding sycophancy, maintaining independence — is critical to the pipeline's trustworthiness.
> *Source: LLM analysis*
