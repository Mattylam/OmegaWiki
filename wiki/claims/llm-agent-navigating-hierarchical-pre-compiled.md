---
title: "LLM agents navigating a hierarchical pre-compiled corpus skill tree outperform passive dense retrieval and agentic RAG on enterprise QA"
slug: llm-agent-navigating-hierarchical-pre-compiled
status: weakly_supported
confidence: 0.6
tags: [rag, agent-navigation, hierarchical-clustering, enterprise-qa, corpus-navigation, knowledge-distillation]
domain: NLP
source_papers: [don-retrieve-navigate-distilling-enterprise-knowledge]
evidence:
  - source: don-retrieve-navigate-distilling-enterprise-knowledge
    type: supports
    strength: moderate
    detail: "Corpus2Skill achieves F1 0.421, factuality 0.612, context recall 0.587 on WixQA, outperforming dense retrieval, RAPTOR, and agentic RAG baselines; primary failure mode is root misrouting (38/62 failures). Evaluated on a single enterprise benchmark."
conditions: "Demonstrated on a single enterprise benchmark (WixQA, 1,513 documents). Assumes corpus has meaningful topical structure for clustering; compile cost must be amortized over many queries. Backtracking affordance provides advantage specifically for multi-hop and cross-branch queries where dense retrieval systematically fails."
date_proposed: 2026-04-27
date_updated: 2026-04-27
---

> [!abstract] Statement
> When an LLM agent navigates a hierarchically organized, pre-compiled corpus tree (rather than querying a dense vector index), it achieves higher factuality and context recall on enterprise QA tasks. The advantage arises from the agent's ability to reason about corpus structure, backtrack from unproductive branches, and combine evidence across sub-trees — capabilities not available to passive retrieval systems.

## Evidence summary

**Supporting evidence:**
- **[[don-retrieve-navigate-distilling-enterprise-knowledge]]** (2026): Corpus2Skill compiles WixQA (1,513 documents) into a 665-node skill hierarchy offline; agent navigates at query time. Achieves factuality 0.612 and context recall 0.587, surpassing all baselines (dense retrieval, hybrid, RAPTOR, agentic RAG) across all quality metrics. Lower token cost than agentic RAG.

> [!info] Conditions and scope
> - **Single-benchmark evidence**: results from WixQA only; enterprise customer-support is structurally well-suited to hierarchical organization, which may not generalize to all corpora
> - **Corpus structure requirement**: k-means clustering assumes topical coherence; flat or heterogeneous corpora may not yield useful navigation hierarchies
> - **Compile-time cost**: the hierarchy must be built offline; the advantage only materialises if queries are frequent enough to amortise compilation
> - **Root routing quality**: the observed 38/62 failure rate from initial misrouting suggests the advantage is sensitive to root summary quality

> [!warning] Counter-evidence
> None yet. The single-benchmark scope is a significant limitation — independent replication on diverse corpora is needed before confidence rises above `weakly_supported`.

## Linked ideas

None yet.

> [!question] Open questions
> - Does hierarchical navigation maintain its advantage over dense retrieval as corpus size scales to 100k+ documents?
> - Is the observed benefit specific to enterprise QA, or does it generalize to open-domain QA where corpus structure is less coherent?
> - How much of the gain is attributable to navigation (backtracking, structure-aware exploration) vs. the hierarchical summarization at compile time?
