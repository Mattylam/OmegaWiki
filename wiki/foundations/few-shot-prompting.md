---
title: "Few-Shot Prompting"
slug: "few-shot-prompting"
domain: "NLP"
status: mainstream
aliases: ["few-shot learning", "k-shot prompting", "demonstration-based prompting"]
first_introduced: "2020"
date_updated: 2026-04-13
source_url: ""
---

## Definition

Few-shot prompting is a technique where a small number of input-output demonstration examples are included in the prompt to guide a language model's behavior on a task, without updating the model's weights. The model leverages in-context learning to infer the task pattern from the demonstrations and apply it to new inputs.

> [!tip] Intuition
> Rather than explaining a task abstractly, you show the model what correct behavior looks like through concrete examples. The model pattern-matches against these demonstrations to produce analogous outputs for new inputs. This is the mechanism that DSPy's BootstrapFewShot automates — instead of a human selecting examples, the optimizer discovers which examples most effectively guide the model.
>
> *Source: LLM analysis*

## Formal notation

A few-shot prompt consists of $k$ demonstrations $\{(x_1, y_1), \ldots, (x_k, y_k)\}$ concatenated with a new input $x_{k+1}$:

$$\text{prompt} = [x_1, y_1, \ldots, x_k, y_k, x_{k+1}]$$

The model generates $\hat{y}_{k+1} = \text{LM}(\text{prompt})$, conditioned on all prior tokens including the demonstrations.

## Key variants

- **Zero-shot**: no demonstrations; task described only via instructions
- **One-shot**: single demonstration example
- **Few-shot (k-shot)**: $k$ demonstrations, typically 3-8
- **Many-shot**: larger demonstration sets (10-50+), enabled by longer context windows
- **Dynamic few-shot**: examples selected at runtime based on similarity to the input
- **Bootstrapped few-shot**: examples selected by an optimizer from successful execution traces

> [!warning] Known limitations
> - Performance is sensitive to example selection, ordering, and formatting
> - Demonstrations consume context window tokens, reducing space for actual input
> - Manual example curation does not scale and becomes stale as data evolves
> - Examples that are idealized or hand-crafted may not represent real failure modes
>
> *Source: LLM analysis*

> [!question] Open problems
> - Optimal example selection strategies that generalize across inputs
> - Understanding why certain demonstrations transfer and others do not
> - Balancing demonstration diversity with relevance
>
> *Source: LLM analysis*

> [!info] Relevance to active research
> Few-shot prompting is the mechanism through which DSPy optimizers improve pipeline modules. BootstrapFewShot selects demonstrations from successful execution traces — examples that actually caused the model to produce correct outputs. In the self-learning project, Phase 1 data collection generates the labeled records that become the candidate pool for few-shot selection in Phase 2. Developer-override records (where the LLM was wrong and the developer corrected it) are particularly high-signal candidates for mandatory few-shot seeds.
>
> *Source: LLM analysis*
