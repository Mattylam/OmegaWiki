---
title: "Scratchpad"
slug: "scratchpad"
domain: "NLP"
status: mainstream
aliases: ["scratch space", "working memory", "rationale field", "inner monologue"]
first_introduced: "2021"
date_updated: 2026-04-13
source_url: ""
---

## Definition

A **scratchpad** is an intermediate working space in which a language model produces free-form text before committing to a final answer. It is not a specific algorithm or module — rather, it describes the general pattern of allowing a model to "think on paper" in the generation process. Scratchpad content is typically placed in a dedicated field (e.g. `rationale`, `thinking`) and may be used by the model internally, stripped from the final response shown to users, or surfaced to the user as a visible reasoning trace.

> [!tip] Intuition
> LLMs are better at multi-step reasoning when allowed to produce intermediate tokens between the question and the answer. The scratchpad is the space where those intermediate tokens live. It is the *architectural pattern*; chain-of-thought, ReAct traces, DSPy rationale fields, and reasoning-mode outputs are all specific instantiations. The key property is that scratchpad tokens influence the final answer through attention, even when they are discarded from the user-visible output.
>
> *Source: LLM analysis*

## Formal notation

Given input $x$, the model produces a sequence $(r_1, r_2, \ldots, r_n, y)$ where $r_i$ are scratchpad tokens and $y$ is the final answer:

$$p(y \mid x) = \int p(r_{1:n} \mid x) \, p(y \mid x, r_{1:n}) \, dr_{1:n}$$

In practice, the model generates left-to-right, so scratchpad tokens are computed first and the answer conditions on them.

## Key variants

- **Chain-of-thought scratchpad**: structured step-by-step reasoning (see [[chain-of-thought-prompting]])
- **ReAct scratchpad**: reasoning interleaved with tool-calling actions (see [[react-prompting]])
- **Hidden rationale**: scratchpad produced internally and stripped before returning the answer (e.g. DSPy `rationale` field, OpenAI o1-style reasoning tokens)
- **Visible rationale**: scratchpad shown to the user as explanation (most CoT variants)
- **Computational scratchpad**: original Nye et al. 2021 framing — models "show their work" for arithmetic and algorithmic tasks

> [!warning] Known limitations
> - Scratchpad content can be unfaithful — the stated reasoning may not reflect the model's actual computation
> - Increases token usage and latency
> - Models may learn to produce confident-looking scratchpad that misleads the final answer
> - Quality depends on whether the scratchpad format matches the task's structure

> [!question] Open problems
> - Measuring faithfulness between scratchpad and final answer
> - Optimal scratchpad formats for different task types
> - Whether scratchpad tokens should be optimised separately from final-answer tokens during training

> [!info] Relevance to active research
> Scratchpads underlie most structured reasoning patterns used in the self-learning project's DSPy-based components. In DSPy, `dspy.ChainOfThought` automatically adds a `rationale` scratchpad field to any signature. During bootstrapping, optimisers can use successful scratchpad traces as demonstrations for future calls — meaning the scratchpad becomes part of the learned prompt. For the Classifier, Prompt Refiner, and Output Evaluator, scratchpad traces also provide the reasoning that developers need to inspect when deciding whether to override an LLM recommendation — so scratchpad faithfulness directly affects the V1 pipeline's interpretability.
>
> *Source: LLM analysis*
