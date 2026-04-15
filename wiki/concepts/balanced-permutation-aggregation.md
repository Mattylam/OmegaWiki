---
title: "Balanced-Permutation Aggregation"
aliases: ["balanced permutation", "rubric permutation debiasing", "permutation-balanced scoring", "position-permutation aggregation"]
tags: [llm-as-judge, debiasing, rubric-based-evaluation, position-bias]
maturity: emerging
key_papers: [am-more-pointwise-pairwise-revealing-position]
first_introduced: "2026"
date_updated: 2026-04-15
related_concepts: [position-bias-llm-judge]
---

## Definition

A debiasing protocol for LLM-as-judge evaluations that exhibit position bias over a list of options (rubric scores, pairwise candidates). The judge is queried multiple times with the options presented in a set of permutations chosen such that each option appears in each position an equal number of times across the set. The final score aggregates (mean, mode, or logprob-weighted) over those queries, both reducing bias and producing a diagnostic (across-permutation variance) that quantifies the residual bias.

> [!tip] Intuition
> If you present a rubric as "0 / 1 / 2 / 3 / 4 / 5" the judge has a positional prior over which slot to pick. Present the same rubric as "5 / 4 / 3 / 2 / 1 / 0" and you expose the opposite prior. Neither single query is trustworthy; their aggregate is. Balanced permutations extend this idea: pick a permutation schedule so that positional priors cancel by construction across the schedule.
> *Source: LLM analysis*

## Formal notation

Given a rubric with $k$ score options $\{s_1, \ldots, s_k\}$ and $k$ positions, construct a permutation set $\Pi = \{\pi_1, \ldots, \pi_m\}$ such that for each score $s_i$ and each position $p$, the count $|\{\pi \in \Pi : \pi(s_i) = p\}|$ is constant. The minimum balanced set has size $k$ (a Latin square row set).

For a candidate output $y$, the debiased score is:

$$\hat{s}(y) = \text{agg}\left(\{J(y, \pi) : \pi \in \Pi\}\right)$$

where $J(y, \pi)$ is the judge's selected score under rubric ordering $\pi$, and `agg` is typically mean or mode.

The across-permutation variance $\text{Var}_{\pi \in \Pi}[J(y, \pi)]$ is itself a diagnostic: low variance ⇒ the judge is robust to position; high variance ⇒ the final score depends heavily on aggregation choice.

## Key variants

- **Full-cycle**: $|\Pi| = k$ cyclic shifts (cheapest balanced design)
- **Latin square**: balanced on both row and column positions — more robust when pairwise position interactions matter
- **Random balanced**: sample a balanced subset when full enumeration is expensive (e.g. list-wise with $k > 10$)
- **Aggregation choice**: mean (most common), mode, logprob-weighted over permutations

## Comparison

| Mitigation | Cost overhead | Handles rubric position bias | Handles pairwise position bias |
|---|---|---|---|
| Single query | 1× | ✗ | ✗ |
| Order swap (pairs only) | 2× | n/a | Partial |
| Balanced permutation | k× (rubric size) | ✓ | ✓ (as full Latin square) |
| Logprob-weighted single | 1× | ✓ (when logprobs available) | n/a |

## When to use

- Any rubric-based pointwise LLM judge before deployment as a benchmark instrument
- Pairwise LLM judges in settings where query cost allows ≥ 2× calls
- Diagnostic pass on a calibration set to quantify the magnitude of position bias present

> [!warning] Known limitations
> - Compute cost grows with rubric size $k$ — a 10-point rubric requires ≥ 10 queries per candidate for a minimum balanced set.
> - Averaging can mask rather than eliminate a bias when bias is asymmetric (systematic rather than positional).
> - Not a replacement for human calibration — reduces one specific bias, leaves others.
> *Source: LLM analysis*

> [!question] Open problems
> - Optimal aggregation for ordinal scales: mean (treats as interval), mode (treats as nominal), or a rank-aware alternative
> - Whether balanced permutations compose cleanly with other debiasing techniques (e.g. self-consistency sampling)
> *Source: LLM analysis*

## Key papers

- [[am-more-pointwise-pairwise-revealing-position]] — introduces balanced-permutation aggregation for rubric-based scoring, demonstrates correlation improvement with humans

> [!tip] My understanding
> For [[golden-evaluator-calibration-baseline]], this should be adopted as the default GE query protocol: every GE score in the calibration study is actually a balanced-permutation aggregate. The across-permutation variance becomes a first-class diagnostic on the calibration card — a GE whose variance is high for some outputs and low for others reveals which outputs sit at decision boundaries the judge finds difficult.
