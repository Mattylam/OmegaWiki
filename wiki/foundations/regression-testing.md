---
title: "Regression Testing"
slug: "regression-testing"
domain: "general"
status: mainstream
aliases: ["regression check", "non-regression testing"]
first_introduced: ""
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Regression_testing"
---

## Definition

Regression testing is re-running functional and non-functional tests to ensure that previously developed and tested software still performs as expected after a change. If the software no longer behaves correctly, that change is called a regression. The practice extends beyond traditional software to any system where changes in one area may unintentionally affect behavior elsewhere.

> [!tip] Intuition
> When you fix a bug or add a feature, you might break something that was working before. Regression testing catches these unintended side effects by re-running existing test cases after every change. In the context of LLM prompt engineering, a prompt change targeting one failure case may degrade outputs for other inputs — regression testing verifies that the fix does not introduce new problems.
> *Source: LLM analysis*

## Formal notation

Given a system $S$ with test suite $T = \{t_1, \ldots, t_n\}$ where all tests pass: $\forall t_i \in T: S(t_i) = \text{pass}$.

After modification $S \rightarrow S'$, regression testing verifies: $\forall t_i \in T: S'(t_i) = \text{pass}$.

A regression is detected when $\exists t_j \in T: S'(t_j) = \text{fail}$.
## Key variants

- **Retest-all**: run the entire test suite after every change
- **Test case prioritization**: run high-risk tests first
- **Selective regression testing**: run only tests affected by the change
- **Visual regression testing**: compare screenshots/outputs pixel-by-pixel
- **LLM output regression**: compare model outputs before and after a prompt change across a held-out set
> [!warning] Known limitations
> - Full regression testing is expensive and time-consuming at scale
> - In LLM systems, "same output" is too strict — semantically equivalent but differently worded outputs should not be flagged
> - No universal threshold for what constitutes a "material" change in LLM outputs
> - Test suite completeness determines regression detection power
> *Source: LLM analysis*

> [!question] Open problems
> - Semantic diff for LLM outputs — distinguishing meaningful changes from cosmetic variation
> - Efficient regression testing for prompt changes across large input populations
> - Automated severity classification of detected regressions
> *Source: LLM analysis*

> [!info] Relevance to active research
> The self-learning pipeline applies regression testing to prompt changes via the Regression Check component. After the Output Evaluator confirms the targeted account's output is satisfactory, the Regression Check runs the modified prompt across all N-1 non-targeted accounts and uses an LLM to detect material, unintended shifts. This is explicitly modeled as selective regression — the developer only triggers it after the targeted fix is validated, avoiding wasted LLM calls on prompt versions the developer will reject. The results are surfaced as evidence, not as a blocking gate — the developer makes the final call.
> *Source: LLM analysis*
