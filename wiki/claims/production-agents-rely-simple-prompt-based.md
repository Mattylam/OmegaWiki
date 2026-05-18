---
title: "Production agents rely on simple prompt-based designs and human evaluation over automated methods"
slug: production-agents-rely-simple-prompt-based
status: supported
confidence: 0.8
tags: [llm-agents, production, empirical, human-in-the-loop, prompt-engineering, evaluation, reliability, survey]
domain: NLP
source_papers: [measuring-agents-production]
evidence:
  - source: measuring-agents-production
    type: supports
    strength: strong
    detail: "306-practitioner survey + 20 case studies: 70% prompt-only, 74% human eval, 68% ≤10 steps, 75% no benchmarks — systematic empirical evidence across 26 industries"
conditions: "Based on 2025 survey data; reflects current reliability constraints and practitioner risk aversion. May shift as automated evaluation methods mature and frontier model reliability improves."
date_proposed: 2026-05-04
date_updated: 2026-05-04
---

> [!abstract] Statement
> In production deployments (2025), LLM-based agents overwhelmingly favor simple, prompt-based architectures (70% no fine-tuning), rely on human evaluation for correctness assessment (74%), operate within short decision chains (68% ≤10 steps), and avoid standard benchmarks (75%). This conservative design posture is driven by the unresolved challenge of agent reliability, not capability limitations.

## Evidence summary

| Source | Type | Strength | Detail |
|--------|------|----------|--------|
| [[measuring-agents-production]] | supports | strong | Systematic empirical study N=306 across 26 industries; all four statistics replicated via dual method (survey + case study) |

> [!info] Conditions and scope
> - Practitioners surveyed: 306, across 26 industries, 2025 snapshot
> - "Production agent" is self-defined by respondents — may include simpler LLM pipelines
> - Reliability-driven conservatism is the stated reason; may not hold for lower-stakes or higher-automation-tolerance domains
> - Temporal: the 70%/74% statistics reflect a moment when automated agent evaluation is nascent; could shift with tooling advances

> [!warning] Counter-evidence
> No contradicting systematic evidence in the wiki yet. Individual published papers often describe more complex, fine-tuned, autonomous agent systems — but these are typically research prototypes rather than production deployments.

## Linked ideas

## Open questions

> [!question] Open questions
> - Does the human-evaluation dominance reflect a capability gap (no good automated tools) or a workflow preference (practitioners trust human review)?
> - Is there evidence that teams who invest in automated evaluation see faster iteration velocity?
> - How does the 74% human eval stat interact with agent scale — does it hold for teams running >1000 agent invocations/day?
