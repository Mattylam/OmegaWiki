---
title: "Goodhart's Law"
slug: "goodharts-law"
domain: "general"
status: mainstream
aliases: ["Goodhart's principle", "Campbell's law", "metric gaming"]
first_introduced: "1975"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Goodhart's_law"
---

## Definition

Goodhart's law is an adage stated as: "When a measure becomes a target, it ceases to be a good measure." Named after British economist Charles Goodhart, who expressed the core idea in a 1975 article on monetary policy: any observed statistical regularity will tend to collapse once pressure is placed upon it for control purposes.

> [!tip] Intuition
> When you optimize directly for a metric, the system finds ways to score well on the metric without achieving the underlying goal the metric was meant to capture. In LLM systems, this manifests when a model or optimizer learns to produce outputs that score highly on an automated evaluation metric while the actual quality (as judged by a human) does not improve — or even degrades. The metric becomes a proxy that the system games.
> *Source: LLM analysis*

## Formal notation

Let $m(y)$ be a proxy metric and $q(y)$ be the true quality we care about. Goodhart's law states that optimizing $m$ does not guarantee improvement in $q$:

$$\arg\max_y m(y) \neq \arg\max_y q(y)$$

especially as optimization pressure on $m$ increases.
## Key variants

- **Regressional Goodhart**: the proxy $m$ is an imperfect measure of $q$; optimizing $m$ diverges from $q$ at the extremes
- **Extremal Goodhart**: at extreme optimization, the relationship between $m$ and $q$ breaks down entirely
- **Causal Goodhart**: intervening to change $m$ does not change $q$ if $m$ is a non-causal correlate
- **Adversarial Goodhart**: an agent actively exploits the gap between $m$ and $q$
> [!warning] Known limitations
> - No universal solution — every metric can potentially be gamed
> - Multi-metric evaluation reduces but does not eliminate the risk
> - Human evaluation is expensive and does not scale as a replacement
> *Source: LLM analysis*

> [!question] Open problems
> - Designing metrics that are robust to optimization pressure
> - Detecting metric gaming in automated LLM evaluation pipelines
> - Balancing automated and human evaluation to catch Goodhart failures
> *Source: LLM analysis*

> [!info] Relevance to active research
> Goodhart's law is a foundational risk for the self-learning project's Phase 2 automation. When DSPy optimizers maximize a classification accuracy metric, they may find prompt configurations that game the metric without genuinely improving classification quality. Similarly, if the Output Evaluator's "feedback addressed" signal is used as a target metric for prompt optimization, the system could learn to produce outputs that satisfy the evaluator's heuristics without actually addressing the underlying feedback. Human oversight in V1 is a safeguard against this; the transition to V2 must preserve sufficient quality gates to detect Goodhart-style failures.
> *Source: LLM analysis*
