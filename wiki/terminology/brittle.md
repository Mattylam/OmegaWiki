---
title: "Brittle"
slug: brittle
aliases: ["brittleness", "brittle prompts", "prompt fragility"]
domain: "NLP"
date_updated: 2026-04-13
---

## Definition

**Brittle** describes the fragility of a system or artefact that performs well under narrow conditions but degrades sharply under small perturbations. In the context of LLM pipelines, a brittle prompt is one whose performance is highly sensitive to exact wording, structure, or formatting — small changes ($\delta$) to the prompt $p$ produce large changes in task performance $\mathcal{L}(f_{p + \delta})$, even when the perturbation is semantically negligible.

## Context

"Brittle" is the canonical one-word critique of manual prompt engineering. Three interconnected failure modes are captured by the term: (1) lack of generalisation across models, pipelines, or domains; (2) reliance on hard-coded strings that do not adapt when the underlying system changes; (3) dependence on artisanal string manipulation that is error-prone and tightly coupled to model-specific quirks. It is frequently paired with [[pervasive]] in DSPy and prompt-optimisation literature — manual prompts are both the dominant practice *and* fragile. For the self-learning project, the brittleness of hand-tuned AIUA prompts is the direct motivating problem: every underwriter-feedback cycle today requires a developer to manually re-tune prompts, with no principled mechanism to verify the change generalises.
