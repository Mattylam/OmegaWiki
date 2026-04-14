---
title: "Pearson Correlation"
slug: "pearson-correlation"
domain: "general"
status: mainstream
aliases: ["Pearson's r", "Pearson product-moment correlation", "PCC"]
first_introduced: "1895"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Pearson_correlation_coefficient"
---

## Definition

**Pearson Correlation** ($r$) is a parametric measure of *linear* correlation between two continuous variables. It quantifies how closely the relationship between them follows a straight line, normalised to the range $[-1, 1]$. Pearson's $r$ assumes both variables are approximately normally distributed and that the relationship is linear; it is sensitive to outliers and to non-linear associations.

> [!tip] Intuition
> Pearson's $r$ is the covariance of the two variables divided by the product of their standard deviations — it is covariance on a dimensionless, normalised scale. A value near $+1$ means the two variables move in lockstep along a straight line; near $-1$ means they move in opposite directions along a straight line; $0$ means no linear relationship. The key subtlety: $r = 0$ does **not** mean "no relationship" — a perfect quadratic relationship will also have $r \approx 0$.
>
> *Source: LLM analysis*

## Formal notation

$$r = \frac{\sum (X_i - \bar{X})(Y_i - \bar{Y})}{\sqrt{\sum (X_i - \bar{X})^2 \sum (Y_i - \bar{Y})^2}} = \frac{\text{Cov}(X, Y)}{\sigma_X \, \sigma_Y}$$

where $X_i, Y_i$ are individual observations and $\bar{X}, \bar{Y}$ are their means.

## Key variants

- **Sample Pearson $r$**: computed from a finite sample
- **Population Pearson $\rho$** (overloaded notation): the true underlying value
- **Partial Pearson correlation**: $r$ between $X$ and $Y$ controlling for a third variable $Z$
- **Point-biserial correlation**: Pearson $r$ where one variable is binary — equivalent and sometimes reported separately

> [!warning] Known limitations
> - Assumes linearity — misses strong non-linear relationships (e.g. a U-shape returns $r \approx 0$)
> - Sensitive to outliers: a single extreme point can substantially shift $r$
> - Assumes both variables are continuous and approximately normal; violation reduces statistical power
> - Not invariant under monotonic transformations (log, rank, etc.) — unlike Kendall's $\tau$ or Spearman's $\rho$

> [!question] Open problems
> - Robust variants that downweight outliers while preserving linear-correlation semantics
> - Inference under high-dimensional correlation matrices (sparse PCC estimation)
> - Whether Pearson or rank-based measures are more appropriate for LLM evaluation metrics

> [!info] Relevance to active research
> Pearson's $r$ is used in LLM evaluation when both axes are continuous and reasonably well-distributed — for example, correlation between LLM-judge confidence scores and human confidence ratings, or between model parameter count and benchmark score across a set of models. For the self-learning project, Pearson is appropriate when comparing continuous signals (e.g. Evaluator confidence score vs. developer-assigned confidence). When the signal is ordinal (pass/fail with reasoning, rank-ordered candidates), Kendall's $\tau$ or Spearman's $\rho$ is more appropriate.
>
> *Source: LLM analysis*
