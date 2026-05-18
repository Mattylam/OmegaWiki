---
title: "Curated agentic skills outperform self-generated skills in agent benchmarks"
slug: curated-skills-outperform-self-generated-skills
status: weakly_supported
confidence: 0.6
tags: [agentic-skills, skill-library, evaluation, continual-learning]
domain: NLP
source_papers: [sok-agentic-skills-beyond-tool-use]
evidence:
  - source: sok-agentic-skills-beyond-tool-use
    type: supports
    strength: moderate
    detail: "SoK survey synthesis across multiple benchmarks shows curated skill libraries substantially improve agent success rates while self-generated (autonomously synthesised) skills may degrade them relative to no-skill baseline"
conditions: "Holds in closed-domain agentic benchmarks with deterministic evaluation harnesses. Scope is uncertain for open-domain tasks where 'curated' is expensive to define or verify."
date_proposed: 2026-04-27
date_updated: 2026-04-27
---

> [!abstract] Statement
> In agentic benchmark evaluations, skills curated by humans or verified expert processes consistently produce higher agent success rates than skills generated autonomously by the agent itself (self-generated / self-synthesised skills). Self-generated skills, while scalable, may degrade task performance below the baseline of no skill library at all.

## Evidence summary

| Source | Type | Strength | Detail |
|--------|------|----------|--------|
| [[sok-agentic-skills-beyond-tool-use]] | supports | moderate | SoK benchmark synthesis; aggregated finding across multiple benchmarks |

> [!info] Conditions and scope
> - Applies primarily to closed-domain benchmarks (GAIA, software engineering tasks, web navigation) with deterministic outcome metrics
> - "Curated" means human-authored or expert-verified skill entries; "self-generated" means skills produced by the agent's own reflection/rewriting without external verification
> - Weaker claim for preference-based or open-domain tasks where curation cost is prohibitive
> - Self-generated skill degradation is not universal — it depends heavily on Write operation quality and feedback signal reliability (see [[skill-memory]] limitations)

> [!warning] Counter-evidence
> - [[memento-skills-let-agents-design-agents]] shows that *active* self-evolving skill memory (with failure attribution + rewriting) achieves +13.7/+20.8 pp improvements over static baseline, which complicates a simple "curated beats self-generated" narrative. The distinction may be *quality of the self-generation process* rather than human curation per se.

## Linked ideas

## Open questions

> [!question] Open questions
> - Does the curated vs. self-generated performance gap shrink as skill synthesis quality improves (better LLMs, better reflection prompts)?
> - Can verification-based self-generation (generate + unit test + filter) close the gap without human curation?
> - Is the degradation observed with self-generated skills caused by skill *quality* or skill *retrieval noise* (wrong skill selected)?
