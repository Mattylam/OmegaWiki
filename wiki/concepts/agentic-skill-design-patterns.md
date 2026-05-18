---
title: "Agentic Skill Design Patterns"
aliases: ["skill design taxonomy", "skill packaging patterns", "representation-scope taxonomy", "skill execution patterns", "skill lifecycle model"]
tags: [agentic-skills, llm-agents, system-design, skill-library, taxonomy]
maturity: emerging
key_papers: [sok-agentic-skills-beyond-tool-use]
first_introduced: "2026"
date_updated: 2026-04-27
related_concepts: [skill-memory]
---

## Definition

A systematic classification of how agentic skills are designed, packaged, and executed within LLM agent systems. [[sok-agentic-skills-beyond-tool-use]] formalises two orthogonal taxonomies:

1. **Seven system-level design patterns** — how skills are packaged and invoked in practice, ordered along an autonomy spectrum:
   - *Metadata-driven progressive disclosure*: skills expose metadata (name, description, triggers) enabling the agent to selectively load full skill content
   - *Executable code skills*: skills as directly runnable programs (Python, shell, etc.) with typed interfaces
   - *Prompt-template skills*: natural-language procedural templates instantiated at runtime
   - *Plan-compiled skills*: offline-synthesised execution plans stored and retrieved as skills
   - *Self-evolving libraries*: skill stores that autonomously update entries via reflection and rewriting (see [[skill-memory]])
   - *Curriculum-scaffolded acquisition*: skills acquired through structured task progression
   - *Marketplace distribution*: third-party skill registries with discovery, rating, and installation flows

2. **Representation × scope taxonomy** — what skills *are* crossed with where they operate:
   - Representation axis: natural language | code | policy (RL policy / FSM) | hybrid
   - Scope axis: web | OS | software engineering | robotics

> [!tip] Intuition
> Think of the design pattern taxonomy as the analogue of architectural patterns in software engineering — "observer", "factory", "strategy" — but for skills. The patterns are not mutually exclusive; real systems mix them (e.g., a marketplace distributes metadata-gated executable code skills that are self-evolving). The representation × scope taxonomy adds orthogonal detail: the *what* (natural language vs. formal) determines governance tractability; the *where* (web vs. robotics) determines environmental feedback richness.

## Formal notation

A skill is formally defined as a 4-tuple:

$$s = (C, \pi, T, R)$$

where:
- $C$: applicability conditions — a predicate over state/context that determines when the skill is eligible
- $\pi$: executable policy — the procedural content (prompt, code, or RL policy) that implements the skill
- $T$: termination criteria — conditions under which skill execution completes (success or failure)
- $R$: reusable interface — typed input/output contract enabling composition

The skill lifecycle model maps how a skill instance evolves through seven stages: *discovery → practice → distillation → storage → retrieval → execution → evaluation*, with feedback loops permitting Write-back mutations at each stage.

## Variants

- **Static curated library**: all four 4-tuple fields are human-authored and frozen; high reliability, low scalability
- **Self-evolving library** (see [[skill-memory]]): $\pi$ and $C$ mutate via reflection after execution; high scalability, reliability depends on Write quality
- **Marketplace skills**: $R$ is exposed as a public API; introduces trust and supply-chain risk (see security section)

## When to use

- Use **metadata-driven progressive disclosure** when skill libraries are large (>50 skills) and context window pressure requires lazy loading
- Use **executable code skills** when governance and auditability are priorities — code is statically analysable, natural language is not
- Use **self-evolving libraries** only in closed environments with deterministic feedback signals; avoid in open-domain or safety-critical settings
- Use **marketplace distribution** with tiered trust enforcement and runtime sandboxing; treat all third-party skills as untrusted inputs

> [!warning] Known limitations
> - The 7-pattern taxonomy is post-hoc (induced from 65 papers); corner cases may not map cleanly — compositional cross-pattern systems are underspecified
> - The formal 4-tuple glosses over important implementation details: how $C$ is evaluated (LLM inference vs. rule-based check), how $T$ handles partial success, how $R$ handles type coercion
> - Representation × scope is incomplete for multimodal and embodied agents; the taxonomy was developed primarily from NLP/code agent literature

> [!question] Open problems
> - Automated synthesis of the 4-tuple from natural-language task descriptions without human annotation
> - Formal compositionality guarantees: when two skills are composed, do their $C$ and $T$ fields compose predictably?
> - Cross-scope skill transfer: can a skill designed for OS scope be adapted to software engineering scope without reauthoring?

## Key papers

- [[sok-agentic-skills-beyond-tool-use]] (2026) — introduces the formal 4-tuple, 7-pattern design taxonomy, and representation × scope taxonomy in a systematic literature review
