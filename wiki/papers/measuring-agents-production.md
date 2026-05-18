---
title: "Measuring Agents in Production"
slug: measuring-agents-production
arxiv: "2512.04123"
venue: "arXiv.org"
year: 2025
tags: [llm-agents, production, empirical-study, survey, reliability, human-in-the-loop, evaluation, prompt-engineering, agent-deployment]
importance: 4
date_added: 2026-05-04
source_type: pdf
s2_id: "87eb81ab187f862091b56bd8f35423d050caaaf6"
keywords: [production agents, agent reliability, human evaluation, prompt engineering, agent deployment, empirical study]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

Despite widespread deployment of LLM-based agents across industries, no systematic empirical study existed characterizing what technical methods actually work in production — the field operated on academic benchmarks and architectural proposals that may not reflect deployment reality. MAP fills this gap with first-hand data from practitioners building real systems.

## Key idea

The first large-scale empirical study of production AI agents: 306 practitioners surveyed + 20 in-depth case study interviews across 26 domains. The central finding is that production agents are consistently built using **simple, controllable, human-overseen approaches** — the opposite of the autonomy-maximizing direction dominant in academic research. Reliability, not capability, is the primary constraint shaping design decisions.

## Method

**Mixed-methods design**:
- Online survey: 306 practitioners across 26 industries
- In-depth interviews: 20 case studies (semi-structured, 1-2 hours each)
- Dimensions investigated: (1) why organizations build agents, (2) how they build them, (3) how they evaluate them, (4) top development challenges

**Key quantitative findings**:
- **68%** of production agents execute ≤10 steps before human intervention
- **70%** rely on prompting off-the-shelf models rather than weight tuning
- **74%** depend primarily on human evaluation for correctness assessment
- **61%** do not maintain baseline comparisons; **75%** do not use benchmarks
- **68%** operate in read-only or sandboxed environments (risk control)
- Primary motivation: productivity/efficiency automation (73% of deployments)

**Reliability mechanisms**: practitioners achieve reliability through *systems-level constraints* (short chains, human checkpoints, sandboxed environments) rather than training or automated evals.

## Results

Production agents deliver measurable impact across diverse industries via conservative engineering discipline. The research-to-deployment gap is real: academic agent research optimizes for capability and autonomy, while production prioritizes controllability and reliability. Human evaluation dominates because automated evaluation of agentic behavior remains unsolved — most teams build custom golden question sets or rely on user feedback / A/B tests.

> [!warning] Known limitations
> - Survey respondents are self-selected practitioners, biasing toward teams comfortable sharing; early-failure deployments are under-represented
> - "Agent" is loosely defined — some surveyed systems may be simple LLM pipelines rather than decision-making agents
> - No longitudinal data: snapshot of 2025 deployment practice; the field is evolving rapidly
> - 26 domains covered but sample sizes per domain are uneven

> [!question] Open questions
> - What does the evaluation stack look like at the few teams (26%) that do use automated evaluation? Are there common patterns?
> - Does the "10 steps before human intervention" constraint reflect fundamental reliability limits or conservative practice that will relax as models improve?
> - How does agent design simplicity interact with task complexity — are there domains where autonomous agents are already reliable?

> [!tip] My take
> This is the most grounded empirical reference for the state of production agents. The core finding — that practitioners default to simplicity + human oversight — is a direct challenge to research agendas focused on longer-horizon autonomy and self-improvement without addressing reliability. The 74% human evaluation statistic is particularly striking: it suggests automated agent evaluation is not just a research gap but a practical bottleneck that matters right now.
>
> The data on 70% prompting off-the-shelf vs fine-tuning is also a useful prior for scoping research — if practitioners overwhelmingly avoid fine-tuning in production, methods that require it (including many memory/adaptation approaches) face an adoption barrier that's worth quantifying.

## Related

- [[production-agent-deployment-patterns]]
- [[human-in-the-loop-learning]]
- [[matei-zaharia]]
- [[lakshya-agrawal]]
- supports: [[production-agents-rely-simple-prompt-based]]
