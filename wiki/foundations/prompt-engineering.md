---
title: "Prompt Engineering"
slug: "prompt-engineering"
domain: "NLP"
status: mainstream
aliases: ["prompt design", "prompt crafting", "prompt tuning"]
first_introduced: "2021"
date_updated: 2026-04-13
source_url: "https://en.wikipedia.org/wiki/Prompt_engineering"
---

## Definition

Prompt engineering is the process of structuring natural language inputs to produce specified outputs from a generative AI model. Context engineering is the related area of software engineering that focuses on management of non-prompt contexts supplied to the model, such as metadata, API tools, and tokens.

> [!tip] Intuition
> Rather than modifying a model's weights, prompt engineering shapes model behavior by controlling what the model reads before generating a response. The quality, structure, and specificity of the prompt directly determine output quality. This makes prompt engineering a form of programming where the "code" is natural language instructions, demonstrations, and constraints.

## Formal notation

A prompt $p$ is a function mapping task specification to model input: $p: \text{task} \rightarrow \text{input\_tokens}$. The model then computes $\text{output} = \text{LM}(p(\text{task}))$. Prompt engineering optimizes $p$ to maximize a quality metric $m$: $p^* = \arg\max_p \mathbb{E}[m(\text{LM}(p(\text{task})))]$.

## Key variants

- **Zero-shot prompting**: task description only, no examples
- **Few-shot prompting**: task description with input-output demonstrations (see [[few-shot-prompting]])
- **Chain-of-thought prompting**: elicits intermediate reasoning steps (see [[chain-of-thought-prompting]])
- **Instruction tuning**: training models to follow prompt instructions more reliably
- **System prompts**: persistent instructions that frame the model's role and constraints
- **Template-based prompting**: structured string templates with variable slots filled at runtime

> [!warning] Known limitations
> - **Brittleness**: small wording changes can produce dramatically different outputs with no principled mechanism for predicting the effect
> - **Non-transferability**: prompts optimized for one model often fail on another model or even a different version of the same model
> - **Scalability**: manual prompt engineering does not scale across many tasks or changing requirements
> - **Opacity**: no formal theory connects prompt structure to output quality — optimization is largely empirical

> [!question] Open problems
> - Automated prompt optimization that replaces manual trial-and-error (see [[prompt-optimization]])
> - Formal frameworks for reasoning about prompt-output relationships
> - Cross-model prompt portability
> - Measuring prompt robustness under input distribution shift
>
> *Source: LLM analysis*

> [!info] Relevance to active research
> Prompt engineering is the manual baseline that automated prompt optimization frameworks like DSPy aim to replace. In the self-learning project, the V1 pipeline's Prompt Refiner generates suggested prompt edits — automating one step of prompt engineering — while Phase 2 targets fully automated optimization via DSPy teleprompters. The brittleness and non-scalability of manual prompt engineering is the core problem motivating the entire self-learning system.
>
> *Source: LLM analysis*
