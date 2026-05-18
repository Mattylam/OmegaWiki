---
title: "Skill Memory"
aliases: ["skill library", "external skill memory", "reusable skill artefacts", "skill folders", "skill-as-memory", "skill-level memory", "skill store"]
tags: [continual-learning, llm-agents, memory-based-rl, externalized-memory, prompt-engineering]
maturity: emerging
key_papers: [memento-skills-let-agents-design-agents, sok-agentic-skills-beyond-tool-use]
first_introduced: "2026"
date_updated: 2026-04-27
related_concepts: []
---

## Definition

A **skill memory** is a finite, growing collection of reusable *skill artefacts* — structured units containing declarative specifications, prompts, and executable code — that serve as the external, non-parametric memory of an LLM agent. Unlike traditional episodic memory that logs raw state-action-reward transitions, each element of a skill memory encapsulates *both behaviour and context*, making it directly re-executable by the frozen LLM.

Formally (per [[memento-skills-let-agents-design-agents]]): a skill memory $M_t = \{c_i\}_{i=1}^{N_t}$ where each $c_i$ is a skill folder containing spec + prompt + code, and the space of all finite skill memories is denoted $\mathcal{M}$.

> [!tip] Intuition
> Traditional episodic memory for LLM agents stores raw interaction traces — "I was in state $s$, took action $a$, got reward $r$." Skill memory stores something more actionable: a *package of behaviour* that the agent can retrieve and re-run. A skill is closer to a function in a library than a log entry. When the agent encounters a new task, it looks up a skill that matches, executes it, and — if the outcome was good — marks the skill as useful; if bad, the skill gets rewritten or a new one gets synthesised. The memory is both the experience log and the executable program.

## Formal notation

An LLM agent policy augmented with skill memory:

$$\pi^\mu(a \mid s, M_t) = \sum_{c \in M_t} \mu(c \mid s, M_t) \, p_{\text{LLM}}(a \mid s, c)$$

where $\mu(c \mid s, M_t)$ is the retrieval policy over skills given current state, and $p_{\text{LLM}}(a \mid s, c)$ is the frozen LLM's decision kernel conditioned on the retrieved skill $c$. The Write operation mutates $M_t$:

$$M_{t+1} = \text{Write}(M_t, s_t, a_t, r_t)$$

Unlike append-only episodic memory, Write here performs failure attribution and *file-level rewriting* of skill folders.

## Variants

- **Passive episodic memory** (Memento 2 baseline): append-only log of raw transitions; Write is a simple concatenation. The retrieval policy selects relevant past episodes as in-context examples.
- **Active skill memory** (Memento-Skills): Write mutates skill folders — rewrites code, prompts, and specs; can delete obsolete skills or synthesise new skills from failure traces. This is the variant contributed by [[memento-skills-let-agents-design-agents]].
- **Reasoning strategy memory** (ReasoningBank, [[reasoningbank-scaling-agent-self-evolving-reasoning]]): Write distills high-level reasoning principles from both success and failure trajectories into titled strategy entries; memory is abstract text rather than executable code. See [[agent-reasoning-memory]].
- **Informational skill** (Corpus2Skill, [[don-retrieve-navigate-distilling-enterprise-knowledge]]): reinterprets skills as navigable knowledge summaries rather than executable procedures; the agent navigates a pre-compiled skill tree to locate relevant documents. See [[hierarchical-corpus-navigation]].
- **Procedural memory** (ProcMem, [Mi et al. 2026]): reusable procedural memory learned via non-parametric PPO; closely related but uses different update mechanism.
- **Case-based reasoning** (DS-Agent, Guo et al. 2024): classical CBR-style retrieval of past cases; predecessor paradigm without automated Write mutations.

## Comparison

| Paradigm | Memory unit | Write operation | LLM weights |
|---|---|---|---|
| Fine-tuning (see [[fine-tuning]]) | Model weights $\theta$ | Gradient update | Mutated |
| Few-shot prompting (see [[few-shot-prompting]]) | In-context demonstrations | None (static) | Frozen |
| Episodic memory | Raw transition logs | Append | Frozen |
| **Skill memory** | Spec + prompt + code folders | Mutate (rewrite / synthesise) | Frozen |
| DSPy BootstrapFewShot (see [[prompt-optimization]]) | Selected few-shot examples | Offline optimisation | Frozen |

## When to use

- **Closed-domain tasks with structured feedback** (benchmarks, code execution, verifiable environments) where the Write step can attribute failure unambiguously
- **Tasks that decompose into reusable sub-procedures** (web search, terminal operations, domain-specific reasoning) — skill libraries benefit from cluster structure
- **Deployment scenarios where fine-tuning is infeasible** due to frozen model APIs, compute constraints, or the need for per-user customisation without weight divergence
- **Agentic systems that should improve over long horizons** (weeks/months of deployment) — skill memory grows and refines continuously

> [!warning] Known limitations
> - **Write reliability**: the reflection step that rewrites skills can itself err; a broken Write operation can degrade the skill library over time
> - **Coverage gaps**: skills learned on one task distribution may not transfer to genuinely novel domains
> - **Scaling**: retrieval latency grows with library size; requires careful indexing (clustering, embeddings) at 100s-1000s of skills
> - **Ground-truth dependency**: the Write step needs a feedback signal to know whether a skill succeeded — weak on preference-based tasks where "correct" is contested
> - **Safety**: skills contain executable code that the agent itself can modify; insufficient sandboxing risks unbounded behaviour mutation

> [!question] Open problems
> - Can skill memory be shared across agents (multi-tenant deployment) without leaking user-specific context?
> - How does the Write operation compare to parameter-efficient fine-tuning (LoRA) in sample efficiency at matched compute?
> - Formal guarantees for Write-mutates-skills (the existing convergence results assume append-only memory)
> - Adversarial robustness of the retrieval policy — can a malicious skill be injected and preferentially routed to?

## Key papers

- [[memento-skills-let-agents-design-agents]] (2026) — introduces skill-as-memory with active Write operation; first to show +13.7/+20.8 pp improvement over static-library ablation on GAIA/HLE
- [[reasoningbank-scaling-agent-self-evolving-reasoning]] (2025) — closely related; uses reasoning strategy principles instead of executable code/specs; see [[agent-reasoning-memory]] for the concept page
- [[don-retrieve-navigate-distilling-enterprise-knowledge]] (2026) — reinterprets skills as informational navigation units; see [[hierarchical-corpus-navigation]] for the concept page
- [[sok-agentic-skills-beyond-tool-use]] (2026) — SoK paper; provides the formal 4-tuple definition, 7-pattern design taxonomy, and lifecycle model that contextualise skill memory within the broader agentic skill design space; see [[agentic-skill-design-patterns]]

> [!tip] My understanding
> Skill memory is the LLM-agent analogue of what a senior engineer has over a junior: not more raw IQ, but a better *cached library of moves* refined over years of production experience. The key methodological move in Memento-Skills is turning Write from a log-append into a mutation — this is what separates "retrieval-augmented LLM" from "self-improving LLM agent." For the self-learning project, the relevant analogue is the Prompt Database + iteration history: approved prompts are persisted skills, rejected attempts are failure traces that could be fed back into a Write operation. Phase 2's DSPy optimisation is a narrower form of this (only the prompt string mutates, not the code or spec), but the conceptual frame is the same.
