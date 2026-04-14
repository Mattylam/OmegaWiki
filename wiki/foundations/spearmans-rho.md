---
title: "Spearman's Rho"
slug: "spearmans-rho"
domain: "general"
status: mainstream
aliases: ["Spearman's ρ", "Spearman rank correlation", "Spearman rank-order correlation"]
first_introduced: "1904"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Spearman%27s_rank_correlation_coefficient"
---

## Definition

**Spearman's Rho** ($\rho$) is a non-parametric statistical measure of rank correlation. It is equivalent to applying Pearson correlation to the *ranks* of the data rather than the raw values — capturing the strength and direction of any **monotonic** relationship between two variables. Like Kendall's $\tau$, it is robust to outliers and makes no assumption of normality or linearity.

> [!tip] Intuition
> Replace each observation with its rank (1st, 2nd, 3rd, ...). Then compute ordinary Pearson correlation on the ranks. That's Spearman's $\rho$. Because ranks are invariant under monotonic transforms, $\rho$ captures "do $X$ and $Y$ go up together in the same order" without caring about whether the relationship is linear. A $\rho$ of 0.8 means the variables preserve most of each other's ordering; $\rho = 1$ means perfectly monotonic in the same direction.
>
> *Source: LLM analysis*

## Formal notation

When there are no ties:

$$\rho = 1 - \frac{6 \sum d_i^2}{n(n^2 - 1)}$$

where $d_i = \text{rank}(X_i) - \text{rank}(Y_i)$ is the difference between the ranks of each observation pair, and $n$ is the number of observations. In general (with ties), it is defined as Pearson correlation applied to the ranked values.

## Key variants

- **Pearson-on-ranks**: the general definition that handles ties correctly
- **Simplified formula (above)**: valid only when all ranks are distinct

> [!warning] Known limitations
> - Less robust than Kendall's $\tau$ on small samples or with many tied ranks — the squared-difference formulation amplifies large disagreements
> - Like all rank correlations, ignores the magnitude of differences — two variables that agree on ordering but disagree on magnitudes still score $\rho = 1$
> - Can be misleading if the relationship has both monotonic and non-monotonic components

> [!question] Open problems
> - Theoretical comparison: when exactly does $\rho$ give different conclusions than Kendall's $\tau$?
> - Robust rank correlations for data with heavy ties or clustering
> - Extensions to partial rank correlation (controlling for a third variable)

> [!info] Relevance to active research
> Alongside Kendall's $\tau$, Spearman's $\rho$ is commonly reported in LLM evaluation papers to measure agreement between automated judges and human rankings. In practice, the two measures give similar conclusions; $\rho$ is often chosen for its familiarity and easier interpretation (it ranges from -1 to 1 on a Pearson-like scale), while $\tau$ is preferred in statistical theory for small-sample robustness. For the self-learning project, either is appropriate for calibrating the Output Evaluator's ranking of candidate prompts against developer rankings — though consistency matters more than the specific choice.
>
> *Source: LLM analysis*
