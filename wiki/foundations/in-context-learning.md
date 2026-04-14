---
title: "In-Context Learning"
slug: "in-context-learning"
domain: "NLP"
status: mainstream
aliases: ["ICL", "learning from demonstrations", "few-shot adaptation"]
first_introduced: "2020"
date_updated: 2026-04-13
source_url: ""
---

## Definition

In-context learning (ICL) is the ability of large language models to adapt to new tasks by conditioning on input-output demonstrations provided in the prompt, without any update to model weights. The model infers the task pattern from the demonstrations and applies it to subsequent inputs within the same context window.

> [!tip] Intuition
> ICL is what makes few-shot prompting work. When you include examples in a prompt, the model does not "learn" in the traditional sense (no gradient updates occur). Instead, it leverages patterns from pre-training to recognize the task structure implied by the demonstrations and generate outputs consistent with that structure. The model's weights are frozen — all adaptation happens through the input context.
>
> *Source: LLM analysis*

## Formal notation

Given demonstrations $D = \{(x_1, y_1), \ldots, (x_k, y_k)\}$ and a new input $x_{\text{new}}$:

$$P(y_{\text{new}} | x_{\text{new}}, D) = \text{LM}(y_{\text{new}} | [D; x_{\text{new}}])$$

The model conditions on the concatenation of demonstrations and new input. No parameter update $\Delta\theta$ occurs — the same model weights $\theta$ are used regardless of the demonstrations provided.

## Key variants

- **Zero-shot ICL**: no demonstrations; the model infers the task from instructions alone
- **Few-shot ICL**: small number of demonstrations (3-8)
- **Many-shot ICL**: larger demonstration sets enabled by long-context models
- **Retrieval-augmented ICL**: dynamically selecting demonstrations based on input similarity
- **Task-specific ICL**: demonstrations curated for a narrow task distribution

> [!warning] Known limitations
> - Performance degrades with poorly chosen or ordered demonstrations
> - The mechanism by which ICL works is not fully understood theoretically
> - Sensitive to surface-level features of demonstrations (formatting, label balance)
> - Bounded by context window length — cannot accommodate unlimited demonstrations
>
> *Source: LLM analysis*

> [!question] Open problems
> - Theoretical understanding of why ICL emerges from pre-training
> - Predicting which examples will be effective for a given input without trial-and-error
> - Relationship between ICL and implicit gradient descent during inference
>
> *Source: LLM analysis*

> [!info] Relevance to active research
> ICL is the foundational mechanism that DSPy's prompt optimization exploits. When DSPy's BootstrapFewShot selects effective demonstrations, it is optimizing the ICL signal — finding the examples that cause the model to best adapt to the target task purely through context conditioning. The self-learning project's Phase 2 optimization pipeline relies on ICL: optimized prompts contain better demonstrations, not better model weights.
>
> *Source: LLM analysis*
