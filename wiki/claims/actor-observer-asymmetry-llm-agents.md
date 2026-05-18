---
title: "Actor-Observer Asymmetry induces perspective-inconsistent fault attribution in multi-agent LLM systems"
slug: actor-observer-asymmetry-llm-agents
status: weakly_supported
confidence: 0.65
tags: [multi-agent, cognitive-bias, fault-attribution, llm-agents, role-playing, reliability]
domain: NLP
source_papers: [taming-actor-observer-asymmetry-agents-dialectical]
evidence:
  - source: taming-actor-observer-asymmetry-agents-dialectical
    type: supports
    strength: moderate
    detail: "Ambiguous Failure Benchmark shows >20% flip rate when actor/observer roles are swapped across most tested LLMs; ReTAS training reduces V-AOA"
conditions: "Measured on structured QA (FinQA) and text-to-SQL (Spider) domains with synthetic failure scenarios; generalization to open-domain agentic tasks unverified"
date_proposed: 2026-05-01
date_updated: 2026-05-01
---

> [!abstract] Statement
> When the same LLM agent operates in an actor role (self-reflection, diagnosing own failures) versus an observer role (auditing another agent's failures), it produces systematically inconsistent attributions for the same incident — actors attribute failures externally, observers attribute them internally. This Actor-Observer Asymmetry affects >20% of cases on a controlled benchmark and undermines the reliability of multi-agent audit pipelines.

## Evidence summary

| Source | Type | Strength | Detail |
|--------|------|----------|--------|
| [[taming-actor-observer-asymmetry-agents-dialectical]] | supports | moderate | AFB benchmark; >20% flip rate across models; V-AOA metric defined and measured |

> [!info] Conditions and scope
> - Evaluated on Ambiguous Failure Benchmark (AFB), synthetic scenarios across 10 domains
> - Attribution categories: binary internal/external — may miss more nuanced attribution patterns
> - Models tested: not specified in abstract; results described as consistent "for most models"
> - Mitigation (ReTAS) validated only on FinQA-TAS and Spider-TAS

> [!warning] Counter-evidence
> No contradicting evidence in the wiki yet. The benchmark is fully synthetic, raising the question of whether the 20% flip rate would replicate on naturalistic agent failure logs.

## Linked ideas

## Open questions

> [!question] Open questions
> - Does AOA persist when agent roles are implicit (e.g., tool-use agents without explicit role-swap instructions)?
> - Is 20% flip rate practically significant — does it translate to measurable failure in downstream task completion?
> - Can perspective-invariant prompting (without re-training) partially mitigate AOA?
