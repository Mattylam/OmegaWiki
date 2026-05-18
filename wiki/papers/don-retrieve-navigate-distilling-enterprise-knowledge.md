---
title: "Don't Retrieve, Navigate: Distilling Enterprise Knowledge into Navigable Agent Skills for QA and RAG"
slug: don-retrieve-navigate-distilling-enterprise-knowledge
arxiv: "2604.14572"
venue: "arXiv.org"
year: 2026
tags: [rag, agent-navigation, knowledge-distillation, hierarchical-clustering, enterprise-qa, corpus-navigation, skill-based-retrieval, agentic-rag]
importance: 3
date_added: 2026-04-27
source_type: pdf
s2_id: "7686a9b2f6c929591bbec8ff19029f14a070a620"
keywords: [hierarchical clustering, agent skills, knowledge navigation, skill directory, offline knowledge distillation]
domain: NLP
code_url: ""
cited_by: []
---

## Problem

RAG treats the LLM as a **passive consumer** of search results: it submits a query, receives top-k documents, and generates. The model never sees how the corpus is organized, what it has not yet retrieved, or which branches of the knowledge base are relevant. This creates two structural failure modes: (1) **no backtracking** — the agent cannot recover from a bad initial query, and (2) **evidence scattering** — multi-hop questions whose answers span distant corpus branches are systematically missed because each retrieval step sees only a local slice.

## Key idea

**Corpus2Skill** shifts the paradigm from *retrieve* to *navigate*: instead of submitting a query to a search index at serve time, an agent receives an explicitly visible, hierarchically organized map of the corpus and navigates it top-down — drilling into relevant branches, backtracking from dead ends, and combining evidence across sub-trees.

The corpus is compiled **offline** into a *hierarchical skill directory*: documents are iteratively clustered, LLM-generated summaries are written at each level, and the result is materialized as a tree of navigable skill files. This tree is static until re-compiled but requires no query-time retrieval infrastructure — the agent operates solely from the pre-built hierarchy and the LLM.

The key reframing: the paper treats skill files as **informational skills** (structured summaries that tell an agent *what to look for* and *where*) rather than **procedural skills** (executable code that tells an agent *how to act*). Navigating the hierarchy is itself an agentic action, not a lookup.

## Method

**Offline compilation pipeline**:
1. Embed all documents; iteratively cluster via k-means until each cluster falls below a document-count threshold
2. At each cluster level, prompt an LLM to generate a summary of the cluster's topic areas, typical question types, and sub-cluster structure
3. Materialize the hierarchy as a directory of `SKILL.md` files — each containing the cluster summary and links to child skills or leaf documents
4. Root skill file provides a bird's-eye view; leaf nodes hold full document IDs

**Serve-time navigation**:
1. Agent receives the root `SKILL.md` (global corpus overview)
2. Agent chooses one or more relevant sub-skills to drill into, receiving their summaries
3. Agent continues drilling until it reaches leaf documents, then retrieves full text by ID
4. Agent generates the final answer from accumulated context, with the ability to backtrack to a parent and explore another branch

**WixQA benchmark**: enterprise customer-support corpus (1,513 documents, Wix product coverage). Compiled to a hierarchy of 665 navigation files and 13 MB of document text.

**Baselines compared**: BM25, dense retrieval, hybrid retrieval, RAPTOR, and agentic RAG (tool-augmented retrieval).

## Results

Corpus2Skill achieves best performance on WixQA across all quality metrics:

| Method | F1 | BLEU | ROUGE-1 | Factuality | Context Recall |
|---|---|---|---|---|---|
| BM25 | — | — | — | ~0.50 | ~0.45 |
| Dense | — | — | — | ~0.55 | ~0.52 |
| RAPTOR | — | — | — | ~0.58 | ~0.56 |
| Agentic RAG | — | — | — | ~0.60 | ~0.57 |
| **Corpus2Skill** | **0.421** | **0.102** | **0.489** | **0.612** | **0.587** |

- Lower token usage and cost than agentic RAG despite better quality
- Primary failure mode: incorrect initial topic routing (38/62 failed queries miss the right top-level skill branch)

> [!warning] Known limitations
> - **Compile cost**: building the hierarchy is LLM-intensive; batch compilation means the skill tree becomes stale as the corpus evolves
> - **Hard clustering**: k-means produces non-overlapping clusters; documents that span multiple topics land in one branch only
> - **Single-path navigation**: current agent navigates depth-first; parallel branch exploration is not implemented
> - **No incremental updates**: adding new documents requires re-running the full compilation pipeline
> - **API token constraints**: deep hierarchies may exceed context limits if the agent drills too many levels simultaneously
> - **Root misrouting**: 38/62 failures trace to the agent selecting the wrong top-level branch at step 1

> [!question] Open questions
> - Can the hierarchy be made updatable incrementally (e.g., inserting new document clusters without full recompile)?
> - How does Corpus2Skill scale to corpora 10× or 100× the WixQA size (1,513 documents)?
> - Would multi-path navigation (beam search over branches) recover from initial routing errors?
> - Does the informational skill paradigm transfer to knowledge bases with very heterogeneous document types?
> - How sensitive is quality to the LLM used for compilation vs. navigation?

> [!tip] My take
> Corpus2Skill is a clean reframing of a known RAG limitation: the "passive consumer" problem. The insight that visible corpus structure enables backtracking is sound and the results are convincing for the single enterprise benchmark tested. The compilation-once / navigate-many design is a practical tradeoff for high-value queries.
>
> The main fragility is the single-path routing step at the root. That 38/62 failures trace to root misrouting suggests the root `SKILL.md` summary is doing a lot of work — quality of the top-level clustering and summary prompt matters enormously. This is also where the "skills as static files" limitation bites hardest: if the root is poorly organized, the whole tree suffers.
>
> The connection to [[skill-memory]] is explicit in the paper. Corpus2Skill is to *informational* skills what Memento-Skills is to *procedural* skills — both replace naive retrieval with a structured, evolvable knowledge representation that the agent actively engages rather than passively queries.
>
> *Source: DeepXiv analysis + LLM*

## Related

- [[hierarchical-corpus-navigation]] — concept page for the navigation paradigm introduced here
- [[skill-memory]] — Corpus2Skill extends the skill-as-memory idea to informational skills; procedural variant is [[memento-skills-let-agents-design-agents]]
- [[retrieval-augmented-generation]] — contrasted foundation; Corpus2Skill replaces passive RAG with active navigation
- [[memento-skills-let-agents-design-agents]] — procedural skill-memory counterpart; Corpus2Skill is the informational analogue
- supports: [[llm-agent-navigating-hierarchical-pre-compiled]]
