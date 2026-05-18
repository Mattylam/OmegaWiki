---
name: "Lakshya A Agrawal"
affiliation: "UC Berkeley"
tags: [prompt-optimization, llm-programming, compound-ai-system, dspy]
homepage: ""
scholar: ""
date_updated: 2026-04-17
---

## Research areas

Prompt optimization for compound AI systems, sample-efficient adaptation of LLMs, evolutionary search over prompt space. Lead author of GEPA, a reflective evolutionary prompt optimizer integrated with the DSPy ecosystem.

## Key papers

- [[gepa-reflective-prompt-evolution-outperform-reinforcement]] (2025) — first author; introduces GEPA (Genetic-Pareto), a reflective prompt evolution algorithm that outperforms GRPO RL post-training by 6–20% with up to 35× fewer rollouts and beats MIPROv2 by >10% across six benchmarks.
- [[measuring-agents-production]] (2025) — co-author; large-scale empirical study of production AI agents, finding 74% of deployments rely on human evaluation

## Recent work

PhD-track researcher at UC Berkeley working on the post-DSPy generation of compound AI system optimization. GEPA is the marquee contribution; integrated as a teleprompter in the broader DSPy ecosystem (`gepa-ai/gepa` repo).

## Collaborators

- [[omar-khattab]] (senior author on GEPA, DSPy lineage)
- [[matei-zaharia]] (UC Berkeley, ML systems)
- [[christopher-potts]] (Stanford, NLP)
- Dan Klein (UC Berkeley, NLP — co-author on GEPA)
- Ion Stoica (UC Berkeley, systems — co-author on GEPA)

> [!tip] My notes
> Primary contributor on the algorithmic side of the prompt-vs-RL post-training comparison. The Pareto-front selection idea in GEPA is the part most likely to outlast the specific GEPA implementation — a reusable diversity-preserving primitive for any LLM-driven evolutionary search.
