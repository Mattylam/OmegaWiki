---
title: "ReAct Prompting"
slug: "react-prompting"
domain: "NLP"
status: mainstream
aliases: ["ReAct", "Reason + Act", "reasoning and acting"]
first_introduced: "2022"
date_updated: 2026-04-13
source_url: ""
---

## Definition

ReAct (Reason + Act) is a prompting paradigm that interleaves reasoning traces (thought steps) with action calls (tool use, retrieval, API calls) and observations (action results) in an iterative loop. The model alternates between thinking about what to do next and executing actions that produce new information, grounding its reasoning in real evidence rather than relying solely on parametric knowledge.

> [!tip] Intuition
> Pure chain-of-thought reasoning can hallucinate facts because it operates only on the model's internal knowledge. Pure action-taking (tool use without reasoning) can be unfocused because the model does not plan. ReAct combines both: the model reasons about what information it needs, takes an action to get it, observes the result, and reasons again. This thought-action-observation loop reduces hallucination and improves task completion on knowledge-intensive tasks.
> *Source: LLM analysis*

## Formal notation

A ReAct trace consists of interleaved steps:

$$\text{Thought}_1 \rightarrow \text{Action}_1 \rightarrow \text{Observation}_1 \rightarrow \text{Thought}_2 \rightarrow \text{Action}_2 \rightarrow \ldots \rightarrow \text{Answer}$$

Each Thought is free-form reasoning, each Action is a structured tool call, and each Observation is the tool's return value injected back into the context.

## Key variants

- **ReAct**: original interleaved reasoning + acting
- **Reflexion**: adds a self-reflection step after task completion to improve future attempts
- **Plan-and-Execute**: separates planning from execution into distinct phases
- **LATS (Language Agent Tree Search)**: tree search over ReAct traces
- **Tool-augmented LLMs**: broader category where models call tools without explicit reasoning traces

> [!warning] Known limitations
> - Context window consumption grows quickly with long action-observation chains
> - Model may enter repetitive loops (same action repeated without progress)
> - Quality depends heavily on tool design — ambiguous or overly broad tools increase hallucinated tool calls
> - Reasoning traces add latency to each step
> *Source: LLM analysis*

> [!question] Open problems
> - Optimal stopping criteria for the reasoning-action loop
> - Recovering from incorrect actions mid-trace without restarting
> - Balancing reasoning depth with action efficiency
> *Source: LLM analysis*

> [!info] Relevance to active research
> The AIUA research agents are agentic systems that retrieve documents, reason over them, and produce structured analysis — following a ReAct-like pattern. The Mock Runner replays these agent calls with modified prompts, testing whether prompt changes improve the agent's reasoning and action quality. Understanding the ReAct paradigm is essential for designing effective prompts for these agents and for interpreting why an agent produced a particular output.
> *Source: LLM analysis*
