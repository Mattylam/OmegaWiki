---
title: "Kendall's Tau"
slug: "kendalls-tau"
domain: "general"
status: mainstream
aliases: ["Kendall's τ", "tau correlation", "Kendall rank correlation"]
first_introduced: "1938"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Kendall_rank_correlation_coefficient"
---

## Definition

**Kendall's Tau** ($\tau$) is a non-parametric statistical measure of correlation that quantifies the strength and direction of association between two **ranked** variables. It operates on ordinal rankings rather than raw values, counting how often pairs of observations agree (concordant) vs. disagree (discordant) in their ordering. This makes it robust to outliers and applicable to non-normal distributions.

> [!tip] Intuition
> Take every possible pair of observations $(i, j)$. If both $X$ and $Y$ rank them the same way (both say $i$ is bigger), the pair is **concordant**. If they disagree, the pair is **discordant**. Kendall's $\tau$ is just the net agreement rate: (concordant − discordant) / total pairs. $\tau = 1$ means perfect rank agreement, $\tau = -1$ means perfect disagreement, $\tau = 0$ means no ordinal relationship. Because it only looks at *which is bigger* rather than *how much bigger*, it doesn't care about outliers or distribution shape.
>
> *Source: LLM analysis*

## Formal notation

$$\tau = \frac{C - D}{\tfrac{1}{2} n(n-1)}$$

where:
- $C$ = number of **concordant** pairs — pairs $(i, j)$ where if $X_i > X_j$ then $Y_i > Y_j$
- $D$ = number of **discordant** pairs — pairs where $X$ and $Y$ rankings disagree
- $n$ = total number of observations
- $\tfrac{1}{2} n(n-1)$ = total number of pairs

## Key variants

- **Kendall's $\tau_a$**: original formulation, does not account for tied ranks
- **Kendall's $\tau_b$**: adjusts for tied ranks; most commonly reported in practice
- **Kendall's $\tau_c$**: adjustment for contingency tables where row/column sizes differ

> [!warning] Known limitations
> - Requires $O(n^2)$ computation in naive form (though $O(n \log n)$ algorithms exist)
> - Less statistically powerful than Pearson when data actually is linear and normal
> - Interpretation of "what counts as a strong $\tau$" is domain-dependent — there is no universal threshold

> [!question] Open problems
> - Optimal sample sizes for reliably estimating $\tau$ at small $n$
> - Robust extensions to multi-dimensional rank correlation
> - Debate over whether $\tau_b$ or Spearman's $\rho$ is more appropriate for ordinal LLM-judge evaluation

> [!info] Relevance to active research
> In LLM evaluation research (particularly the "agent-as-a-judge" literature), Kendall's $\tau$ is the standard tool for measuring how well an automated evaluator preserves the relative ordering of responses compared to human judges. Typical reported values: $\tau \approx 0.5$–0.6 for GPT-4-as-a-judge vs human rankings; multi-evaluator ensembles add a few points on top. For the self-learning project, Kendall's $\tau$ is the right metric to track when calibrating the Output Evaluator against human-reviewed prompts: if the evaluator's ranking of candidate prompts disagrees with the developer's ranking, the automated pipeline will optimise for the wrong signal. Preferred over Pearson for this purpose because Evaluator outputs are ordinal (pass/fail with reasoning), not continuous.
>
> *Source: LLM analysis*
