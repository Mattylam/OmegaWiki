# Query Pack (general)

_Auto-generated compressed context. Do not edit._

## Claims (31 total)
- [supported] Integrating LLMs with deterministic symbolic solvers materially improves faithfulness of logical reasoning over LLM-only baselines (conf: 0.85)
- [supported] Natural-language policy and compliance rules can be formalised into SMT-LIB constraints with LLM + human assistance, enabling runtime enforcement that reduces violations without sacrificing task accuracy (conf: 0.75)
- [weakly_supported] Bootstrapping produces better few-shot demonstrations than hand-written examples (conf: 0.65)
- [supported] Continual learning for LLM agents can be achieved via external memory evolution without parameter updates (conf: 0.75)
- [supported] Rank correlation alone is insufficient for evaluating LLM judges; per-item agreement (Cohen's κ) relative to the inter-human distribution is required (conf: 0.8)
- [weakly_supported] Decomposer quality can be measured automatically via completeness, correctness, and semantic-entropy metrics, enabling decomposer benchmarking and fine-tuning without human annotation (conf: 0.65)
- [supported] Decomposing long-form generation into atomic facts enables fine-grained factuality evaluation that can be automated to within 2% of human agreement (conf: 0.8)
- [weakly_supported] Routing LLM autoformalization through a domain-specific intermediate DSL (with typed interpreter) materially improves translation robustness and auditability over direct LLM-to-solver generation (conf: 0.7)
- [supported] Compiled DSPy programs outperform hand-crafted prompt chains (conf: 0.8)
- [supported] Encoder-based evaluation matches LLM judge performance at drastically lower compute for reference-based answer correctness (conf: 0.75)
- [supported] Atomic-fact-based factuality metrics (FActScore and kin) are materially sensitive to the decomposition method used; decomposer is a first-class pipeline component that must be independently calibrated (conf: 0.8)
- [supported] LLM judges exhibit systematic biases across 12 distinct categories, with robustness varyi
## Open Gaps
_Auto-generated open questions. Do not edit._
- [claim/actor-observer-asymmetry-llm-agents] Actor-Observer Asymmetry induces perspective-inconsistent fault attribution in multi-agent LLM systems (status: weakly_supported)
- [claim/bootstrapping-produces-better-demonstrations-than-hand-written] Bootstrapping produces better few-shot demonstrations than hand-written examples (status: weakly_supported)
- [claim/curated-skills-outperform-self-generated-skills] Curated agentic skills outperform self-generated skills in agent benchmarks (status: weakly_supported)
- [claim/decomposer-quality-can-be-measured-automatically] Decomposer quality can be measured automatically via completeness, correctness, and semantic-entropy metrics, enabling decomposer benchmarking and fine-tuning without human annotation (status: weakly_supported)
- [claim/distilling-reasoning-strategies-failed-successful-agent] Distilling generalizable reasoning strategies from both failed and successful agent experiences outperforms success-only or raw trajectory memory (status: weakly_supported)
- [claim/dsl-mediated-autoformalization-improves-translation-robustness] Routing LLM autoformalization through a domain-specific intermediate DSL (with typed interpreter) materially improves translation robustness and auditability over direct LLM-to-solver generation (status: weakly_supported)
- [claim/inference-time-agentic-prompt-refinement-textual] Inference-time agentic prompt refinement with textual feedback outperform
## Papers (32 total)
- [5] DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines (NLP)
- [4] When AIs Judge AIs: The Rise of Agent-as-a-Judge Evaluation for LLMs (NLP)
- [5] GEPA: Reflective Prompt Evolution Can Outperform Reinforcement Learning (NLP)
- [4] Memento-Skills: Let Agents Design Agents (NLP)
- [3] Neuro-Symbolic Compliance: Integrating LLMs and SMT Solvers for Automated Financial Legal Analysis (NLP)
- [3] Solver-Aided Verification of Policy Compliance in Tool-Augmented LLM Agents (NLP)
- [3] DecMetrics: Structured Claim Decomposition Scoring for Factually Consistent LLM Outputs (NLP)
- [5] FActScore: Fine-grained Atomic Evaluation of Factual Precision in Long Form Text Generation (NLP)
- [4] Proof of Thought: Neurosymbolic Program Synthesis allows Robust and Interpretable Reasoning (NLP)
- [4] ReasoningBank: Scaling Agent Self-Evolving with Reasoning Memory (NLP)
- [4] A Closer Look at Claim Decomposition (NLP)
- [3] Don't Retrieve, Navigate: Distilling Enterprise Knowledge into Navigable Agent Skills for QA and RAG (NLP)
- [4] Judge's Verdict: A Comprehensive Analysis of LLM Judge Capability Through Human Agreement (NLP)
- [4] Justice or Prejudice? Quantifying Biases in LLM-as-a-Judge (NLP)
- [5] Logic-LM: Empowering Large Language Models with Symbolic Solvers for Faithful Logical Reasoning (NLP)
## Recent Relationships (173 total)
  papers/automatic-prompt-optimization-techniques-exploring-potential --supports--> concepts/reflective-prompt-evolution
  papers/automatic-prompt-optimization-techniques-exploring-potential --supports--> concepts/synthetic-boundary-case-optimization
  papers/reasoningbank-scaling-agent-self-evolving-reasoning --supports--> concepts/agent-reasoning-memory
  papers/reasoningbank-scaling-agent-self-evolving-reasoning --supports--> claims/distilling-reasoning-strategies-failed-successful-agent
  papers/reasoningbank-scaling-agent-self-evolving-reasoning --supports--> claims/continual-learning-via-external-memory
  papers/reasoningbank-scaling-agent-self-evolving-reasoning --derived_from--> foundations/react-prompting
  papers/reasoningbank-scaling-agent-self-evolving-reasoning --derived_from--> foundations/retrieval-augmented-generation
  papers/reasoningbank-scaling-agent-self-evolving-reasoning --extends--> concepts/skill-memory
  papers/don-retrieve-navigate-distilling-enterprise-knowl
