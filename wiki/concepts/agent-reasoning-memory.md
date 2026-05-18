---
title: "Agent Reasoning Memory"
aliases: ["reasoning memory", "reasoning strategy memory", "ReasoningBank", "reasoning strategy distillation", "experience distillation memory"]
tags: [llm-agents, agent-memory, self-evolution, failure-learning, continual-learning, experience-distillation]
maturity: emerging
key_papers: [reasoningbank-scaling-agent-self-evolving-reasoning]
first_introduced: "2025"
date_updated: 2026-04-24
related_concepts: [skill-memory]
---

## Definition

**Agent reasoning memory** is an external, non-parametric memory system that stores distilled, generalizable reasoning strategies derived from an agent's past task experiences (both successes and failures). Unlike episodic memory (which logs raw state-action trajectories) or skill memory (which stores executable code/prompt/spec artefacts), reasoning memory stores abstract, human-readable reasoning principles — titled entries that capture *why* certain approaches worked or failed and *how* to reason differently in analogous future situations.

Each memory item in a reasoning memory system typically has:
- **Title**: a short label identifying the strategy
- **Description**: the triggering context (when to apply this strategy)
- **Content**: the reasoning principle itself (what to do / what to avoid)

At test time, the agent retrieves relevant memory items given the current task context and uses them to guide reasoning. After task completion, the agent synthesises new memory items from its trajectory via LLM-as-a-judge self-evaluation.

> [!tip] Intuition
> Standard LLM agents are stateless between tasks: they start fresh every time. Episodic memory gives them a diary; skill memory gives them a toolbox. Reasoning memory gives them a *lessons-learned file*. The key distinction is abstraction level: instead of replaying "last time I was in state $s$ I did $a$" (episodic) or "use this subroutine" (skill), reasoning memory says "last time I failed on this class of task because I didn't verify intermediate steps — so always verify." The abstraction enables transfer: the principle applies to new tasks, not just the original episode.
>
> Crucially, reasoning memory mines *failures* — not just successes. Prior work (success-only skill libraries like AWM, Synapse) throws away half the information available in an agent's experience. ReasoningBank demonstrates that failure-distilled strategies provide the contrastive signal that makes the memory genuinely useful.
>
> *Source: LLM analysis*

## Formal notation

Let $\mathcal{B}$ denote the reasoning memory (bank). Each memory item $m_i \in \mathcal{B}$ is a triple:

$$m_i = (\text{title}_i, \text{description}_i, \text{content}_i)$$

The agent's policy conditioned on retrieved memories:

$$\pi^\mathcal{B}(a \mid s) = p_{\text{LLM}}\bigl(a \mid s, \text{Retrieve}(s, \mathcal{B})\bigr)$$

where $\text{Retrieve}(s, \mathcal{B}) = \{m_i : \text{sim}(s, m_i) > \tau\}$ is an embedding-similarity retrieval. The Write (memory synthesis) operation after trajectory $\tau$ with self-judged outcome $o \in \{\text{success}, \text{failure}\}$:

$$\mathcal{B}_{t+1} = \text{Write}(\mathcal{B}_t, \tau, o)$$

where Write uses an LLM to extract reasoning principles from $\tau$ conditioned on $o$, then merges them into $\mathcal{B}_t$.

## Variants

- **Success-only strategy distillation** (AWM, Synapse baselines): Write only processes successful trajectories; failure information is discarded. Suboptimal — misses corrective signals.
- **Full experience distillation** (ReasoningBank): Write processes both success and failure trajectories, extracting generalizable principles from each. The primary variant introduced by [[reasoningbank-scaling-agent-self-evolving-reasoning]].
- **Memory-aware test-time scaling (MaTTS)**: augments the Write step by generating $k$ diverse trajectories per task before synthesis, providing richer contrastive signals for higher-quality memory distillation. Creates a feedback loop: better memory → more effective scaling → richer experiences → better memory.

## Comparison

| Paradigm | Memory unit | Failure signal used | LLM weights | Abstraction level |
|---|---|---|---|---|
| Episodic memory | Raw trajectory logs | Implicitly (via full trace) | Frozen | Low (episode-level) |
| [[skill-memory]] | Spec + prompt + code folders | Via Write mutation | Frozen | Medium (procedural) |
| **Agent reasoning memory** | Titled reasoning principles | Explicitly (distilled) | Frozen | High (strategic) |
| Fine-tuning (see [[fine-tuning]]) | Model weights $\theta$ | Via gradient | Mutated | Implicit |

## When to use

- **Multi-step interactive tasks** where reasoning errors compound and failure attribution is tractable (web navigation, coding agents, tool-use agents)
- **Deployment scenarios with diverse task streams** where abstract principles transfer better than episode-specific logs
- **Settings where failure is common and informative** — the benefit over success-only memory is proportional to the information content of failure traces
- **When fine-tuning is infeasible** (frozen API models, per-user customisation) and richer-than-skill memory is needed

> [!warning] Known limitations
> - **Judge reliability**: memory quality depends on LLM-as-a-judge correctness; noisy reward signals corrupt the bank over time (analogous to reward hacking in RL)
> - **Retrieval scalability**: embedding-based retrieval degrades as memory grows without consolidation or indexing
> - **Memory collapse risk**: without pruning/merging policies, the bank may accumulate contradictory or redundant entries
> - **Transfer scope**: strategies distilled from one domain (web navigation) may not transfer to genuinely different domains (mathematical reasoning)
> - **Open-ended tasks**: self-evaluation requires some notion of correctness; preference-based or open-ended tasks lack this grounding

> [!question] Open problems
> - How to detect and resolve contradictions between memory entries as the bank grows?
> - Can reasoning memories be shared across agents / users without privacy leakage?
> - What is the optimal granularity of reasoning principles — too abstract and they're useless, too specific and they don't transfer?
> - Formal convergence guarantees analogous to Memento-Skills' SRDP analysis for reasoning-strategy-level Write operations?
> - Does memory-aware test-time scaling exhibit diminishing returns at high $k$, and where is the optimal compute allocation?

## Key papers

- [[reasoningbank-scaling-agent-self-evolving-reasoning]] (2025) — introduces agent reasoning memory as ReasoningBank; demonstrates 48.8% success on WebArena and 34.2% relative gain over success-only baselines; introduces MaTTS
