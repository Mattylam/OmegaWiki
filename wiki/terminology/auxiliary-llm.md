---
title: "Auxiliary LLM"
slug: auxiliary-llm
aliases: ["expert LLM", "optimizer LLM", "expert language model", "auxiliary language model"]
domain: "NLP"
date_updated: 2026-04-17
---

## Definition

An **auxiliary LLM** is a secondary language model used to support and automate the process of prompt optimization for a primary *solver* LLM. The solver LLM performs the downstream task (e.g. answering questions, classifying text); the auxiliary LLM performs supporting functions — failure diagnosis, prompt generation/editing, example clustering, feedback aggregation, candidate scoring — whose purpose is to improve the solver's performance without any weight updates.

## Context

Four canonical roles appear across the prompt-optimization literature:

- **Feedback and diagnosis** — the auxiliary LLM analyses *wrong examples* where the solver failed, explains why the solution was incorrect, and proposes prompt additions that would fix the error. This is the central move in [[reflective-prompt-evolution]] and its instantiation [[gepa-reflective-prompt-evolution-outperform-reinforcement|GEPA]].
- **Prompt generation and editing** — the auxiliary LLM produces specific sections of a structured prompt (e.g. "Introduction", "Tricks", "Corner Cases") designed to mimic a human-engineered prompt, or applies proposed edits to an existing prompt.
- **Clustering and aggregation** — in [[task-facet-learning-structured-approach-prompt|UniPrompt]], an auxiliary ("expert") LLM clusters training examples by topic or feedback type and aggregates multiple mini-batch feedbacks into generalizable task concepts, enabling [[sectioned-prompt-optimization|sectioned prompt optimization]].
- **Scoring** — some methods use an auxiliary LLM specifically to score the effectiveness of a current prompt before editing it.

Models used in this capacity include GPT-4 (commonly called the *expert LLM*) and fine-tuned smaller models such as Llama2-13B when a specialized section-generator is needed. The goal across all roles is to avoid the cumbersome manual process of human prompt engineering by automating facet identification and error correction.

An auxiliary LLM is structurally distinct from the solver — it reads the solver's trajectory, not the end-user's input — and its capability ceiling bounds the quality of the optimized prompt. This coupling is the primary limitation flagged in [[reflective-prompt-evolution]] and [[meta-prompt-optimization]]: a weaker auxiliary LLM stalls optimization regardless of solver capability.
