---
title: "SoK: Agentic Skills -- Beyond Tool Use in LLM Agents"
slug: sok-agentic-skills-beyond-tool-use
arxiv: "2602.20867"
venue: ""
year: 2026
tags: [agentic-skills, skill-library, skill-taxonomy, llm-agents, security, survey, sok]
importance: 3
date_added: 2026-04-27
source_type: pdf
s2_id: ""
keywords: [agentic skills, skill lifecycle, design patterns, representation-scope taxonomy, supply-chain security, skill evaluation]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

LLM agents increasingly need reusable procedural capabilities — "skills" — to execute long-horizon workflows reliably. Despite rapid proliferation of skill-based systems (skill libraries, marketplace distributions, self-evolving libraries), there is no unified framework for what a skill *is*, how it should be designed, acquired, evaluated, or secured. Definitions are fragmented across the cognitive architecture, RL, and software engineering literatures, and security risks introduced by the skill layer are poorly characterised.

## Key idea

Systematise the agentic skill layer via:
1. A **formal 4-tuple definition** — each skill is $(C, \pi, T, R)$: applicability conditions, executable policy, termination criteria, reusable interface
2. A **7-stage lifecycle model** — discovery → practice → distillation → storage → retrieval → execution → evaluation, with feedback loops enabling continuous refinement
3. A **7-pattern design taxonomy** — how skills are packaged/executed in practice (metadata-driven progressive disclosure, executable code skills, self-evolving libraries, marketplace distribution, etc.)
4. A **representation × scope taxonomy** — what skills *are* (natural language / code / policy / hybrid) crossed with where they operate (web / OS / software engineering / robotics)
5. A **security threat model** grounded by the ClawHavoc case study

## Method

Bottom-up iterative methodology: 65 papers curated, analysed via feature mapping and clustering to identify patterns across the skill lifecycle. Two orthogonal taxonomies derived:

- **Design taxonomy**: seven patterns along an autonomy spectrum, from human-curated static libraries to fully autonomous self-evolving libraries and marketplace distributions
- **Representation × scope**: natural language skills (easy to author, hard to govern) vs. formal skills (code/policy — harder to author, stronger governance guarantees)

Security analysis structured as a threat model covering skill metadata, payload, and execution environment vectors. ClawHavoc case study: ~1,200 malicious skills injected into a major agent marketplace, exfiltrating API keys, credentials, and browser state at scale.

Evaluation framework: five dimensions — correctness, robustness, efficiency, generalisation, safety — anchored to deterministic, outcome-based harnesses. Benchmark evidence: curated skills improve agent success rates substantially; self-generated skills can degrade them.

## Results

- Curated skills consistently outperform self-generated skills in benchmark evaluations
- Formal skill representations (code, policy) offer stronger governance and supply-chain resilience than natural-language skills, at higher authoring cost
- Self-evolving libraries scale well but sacrifice performance
- ClawHavoc demonstrates that skill marketplace trust models are currently insufficient
- Deterministic evaluation harnesses are necessary; LLM-as-judge evaluation of skills is unreliable

## Related

- [[skill-memory]] — concept page for skill libraries; this paper substantially extends the design space and adds security/lifecycle framing
- [[agentic-skill-design-patterns]] — new concept formalising the 4-tuple definition and 7-pattern taxonomy introduced here
- [[memento-skills-let-agents-design-agents]] — closely related: introduces self-evolving skill libraries (one of the 7 patterns)
- [[reasoningbank-scaling-agent-self-evolving-reasoning]] — reasoning strategy variant of skill memory
- [[don-retrieve-navigate-distilling-enterprise-knowledge]] — informational skills / corpus navigation variant
- supports: [[curated-skills-outperform-self-generated-skills]]

> [!warning] Limitations
> - SoK paper: no new empirical benchmark results of its own; evidence synthesis depends on existing benchmarks, some of which have incomparable setups
> - Formal definition (4-tuple) is post-hoc — retrofitted onto existing systems rather than derived from first principles; edge cases (compositional skills, hybrid representations) may not fit cleanly
> - Security threat model is qualitative; no formal adversarial robustness guarantees
> - Coverage biased toward English-language NLP/code agent papers; robotics and CV skill systems underrepresented

> [!question] Open questions
> - Can skill verification be automated at scale without human-in-the-loop audits?
> - Formal convergence guarantees for self-evolving skill libraries under distribution shift?
> - How do trust tiers interact with compositional skill graphs — does trust propagate transitively?
> - Can natural-language skill governance be improved via constrained generation or formal specification extraction?

> [!tip] My take
> A useful reference paper for anyone building skill-based agent systems — primarily for the vocabulary (the 4-tuple, the 7-pattern taxonomy, the representation × scope framing) and the security analysis, which is the most novel contribution. The empirical conclusion that curated skills beat self-generated ones aligns with the Memento-Skills finding that uncontrolled Write operations can degrade a skill library, and adds useful corroboration from a wider literature scan. The ClawHavoc case study is practically important and underrepresented in the ML literature. Not a methods paper — don't cite for a benchmark number; cite for the framework and the security argument.
