---
title: "Ordinal Ranking"
slug: "ordinal-ranking"
domain: "general"
status: mainstream
aliases: ["rank ordering", "ordinal ordering", "rank-based comparison"]
first_introduced: ""
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Level_of_measurement#Ordinal_scale"
---

## Definition

**Ordinal ranking** is a method of ordering items by their *relative position* (1st, 2nd, 3rd, ...) without assuming anything about the magnitude of differences between them. An ordinal ranking captures *which is better* but not *how much better* — preserving the order of observations while discarding information about their absolute scale.

> [!tip] Intuition
> Given three model responses A, B, C that a human judge ranks as **B > A > C**, an automated evaluator that ranks them **B > C > A** has *partially* preserved the ordering — B is still first, but A and C are swapped. An ordinal analysis asks "how close are these two orderings?" (answered by rank-correlation metrics like Kendall's $\tau$ or Spearman's $\rho$) rather than "how wrong are the scores in absolute terms" (which would require cardinal, interval-level data).
>
> *Source: LLM analysis*

## Formal notation

An ordinal ranking on a set $\{x_1, \ldots, x_n\}$ is a permutation $\pi: \{1, \ldots, n\} \to \{1, \ldots, n\}$ assigning a rank to each item. Two rankings $\pi_1, \pi_2$ can be compared via Kendall's $\tau(\pi_1, \pi_2)$ or Spearman's $\rho(\pi_1, \pi_2)$, with $\tau = \rho = 1$ iff $\pi_1 = \pi_2$.

## Key variants

- **Strict ranking**: every item gets a unique rank, no ties
- **Ranking with ties**: tied items receive the same rank (or an averaged rank)
- **Partial ranking**: only some pairs are ordered; others are unknown (common in human judgement with incomplete comparisons)
- **Top-k ranking**: only the top-k items are ordered; the rest are treated as an unordered tail
- **Pairwise preference**: rather than full ranking, only pairwise comparisons are collected (can be aggregated into a ranking via e.g. Bradley-Terry)

> [!warning] Known limitations
> - Discards magnitude information that may matter for decisions (a response with quality 0.95 vs. 0.90 and one with 0.95 vs. 0.05 both rank the same)
> - Ties are ambiguous and handling them requires an extra convention
> - Not suitable when the scale of differences matters (e.g. safety thresholds, cost differences)

> [!question] Open problems
> - Aggregating partial rankings from multiple judges — Kemeny ranking is NP-hard
> - When should ordinal analysis be preferred over cardinal analysis in LLM evaluation?
> - Efficient rank-aggregation under noisy, incomplete, or adversarial judges

> [!info] Relevance to active research
> Ordinal ranking is the natural frame for LLM-as-judge evaluation because automated judges are typically more reliable at relative judgements ("which is better") than at absolute scoring ("how good on a scale of 1–10"). For the self-learning project, the Output Evaluator's outputs are inherently ordinal: a boolean "feedback addressed" verdict is a 2-rank ordering, and comparing candidate prompts is a multi-way ranking. Calibrating the Evaluator against developer judgements should therefore use rank-correlation metrics ([[kendalls-tau]], [[spearmans-rho]]) rather than Pearson's $r$.
>
> *Source: LLM analysis*
