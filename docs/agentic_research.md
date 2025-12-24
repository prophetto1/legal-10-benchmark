# Legal-10 Agentic Benchmark Research

**Internal development notes — not listed in navigation**

---

## Overview

This document catalogs research for developing **Legal-10 Agentic** (S1-S2), an English-language legal agent benchmark adapted from LegalAgentBench methodology.

---

## Benchmark Architecture Decision

### Legal-10 Structure

```
┌─────────────────────────────────────────────────────────┐
│              LEGAL-10 BENCHMARK                         │
├─────────────────────────────────────────────────────────┤
│  CORE (Required) - 8 Skills                             │
│  ├── CB: S7, S8, S9, S10                                │
│  └── RAG: S3, S4, S5, S6                                │
│  → Standard completion + long context                   │
│  → Single connection: CB first → then RAG               │
│  → No cross-contamination (stateless per instance)      │
├─────────────────────────────────────────────────────────┤
│  AGENTIC (Optional) - 2 Skills                          │
│  └── AG: S1, S2                                         │
│  → Requires tool use / function calling                 │
│  → Separate benchmark run                               │
└─────────────────────────────────────────────────────────┘
```

### Scoring

| Model | Core (8) | Agentic (2) | Full (10) |
|-------|----------|-------------|-----------|
| GPT-4 | 78% | 70% | 76% |
| Claude | 80% | 72% | 78% |
| Llama-3 | 75% | N/A | 75%* |

*Models without tool-use get Core score only

---

## LegalAgentBench Methodology (Reference)

**Paper:** [arxiv.org/abs/2412.17259](https://arxiv.org/abs/2412.17259) (ACL 2025)

### Framework Components

| Component | LegalAgentBench (Chinese) | Legal-10 Agentic (English) |
|-----------|---------------------------|----------------------------|
| Corpora | 17 (14 tabular + 3 retrieval) | Adapt from HuggingFace |
| Tools | 37 specialized tools | Build for English legal DBs |
| Tasks | 300 annotated | Combine from HF datasets |
| Jurisdiction | Chinese civil law | US/UK common law |

### Task Construction Framework (6 Steps)

1. **Planning Tree Construction** — Model tool call dependencies as hierarchical tree
2. **Path Selection** — Extract solution paths from 1-hop to 5-hop complexity
3. **Entity Selection** — Validate initial entities complete intended paths
4. **Question Rewriting** — Transform templates into natural language (GPT-4)
5. **Answer Generation** — Programmatically extract via parameterized tool chains
6. **Human Verification** — Expert legal professionals validate all Q&A

### Task Distribution

| Complexity | Count |
|------------|-------|
| 1-hop | 80 |
| 2-hop | 80 |
| 3-hop | 60 |
| 4-hop | 40 |
| 5-hop | 20 |
| Writing | 20 |
| **Total** | **300** |

### Tool Categories

| Category | Count | Function |
|----------|-------|----------|
| Text Retrievers | 3 | Embedding-based document search |
| Database Tools | 28 | Query structured legal data |
| Mathematical Tools | 5 | Arithmetic, sorting, aggregation |
| System Tools | 1 | "Finish" tool for final answers |

### Evaluation Metrics

- **Success Rate** — Keyword matching for final answers
- **Progress Rate** — Intermediate step keywords (key_middle)
- **BERT-Score** — Semantic similarity

### Key Findings

- GPT-4o achieved 79.08% success rate (ReAct method)
- Success declined from 93% (1-hop) to 61% (5-hop)
- LLMs struggle with: legal terminology, deep article interpretation, tool argument specification

---

## HuggingFace English Legal Datasets

### Corpus Sources

| Dataset | HuggingFace Link | Content | Agentic Use |
|---------|------------------|---------|-------------|
| **Caselaw Access Project** | [free-law/Caselaw_Access_Project](https://huggingface.co/datasets/free-law/Caselaw_Access_Project) | 6.7M US court decisions | Case retrieval corpus |
| **COLD Cases** | [harvard-lil/cold-cases](https://huggingface.co/datasets/harvard-lil/cold-cases) | Reformatted CourtListener | Structured case DB |
| **Pile of Law** | [pile-of-law/pile-of-law](https://huggingface.co/datasets/pile-of-law/pile-of-law) | Court opinions, briefs, statutes | Multi-source legal text |
| **CLERC** | [jhu-clsp/CLERC](https://huggingface.co/papers/2406.17186) | 1.84M federal cases, 20.7M citations | Citation retrieval |
| **CUAD** | [theatticusproject/cuad-qa](https://huggingface.co/datasets/theatricusproject/cuad-qa) | 510 contracts, 41 clause types | Contract analysis |
| **LegalBench** | [nguha/legalbench](https://huggingface.co/datasets/nguha/legalbench) | 162 reasoning tasks | Multi-hop reasoning |
| **BSARD** | [maastrichtlawtech/bsard](https://huggingface.co/datasets/maastrichtlawtech/bsard) | Belgian statutes (French) | Statute retrieval pattern |
| **FairLex** | [coastalcph/fairlex](https://huggingface.co/datasets/coastalcph/fairlex) | Swiss/EU court decisions | Multilingual extension |
| **HFforLegal Case-law** | [HFforLegal/case-law](https://huggingface.co/datasets/HFforLegal/case-law) | Multi-country case law | International cases |
| **IL-TUR** | [Exploration-Lab/IL-TUR](https://huggingface.co/datasets/Exploration-Lab/IL-TUR) | Indian statute identification | Statute lookup pattern |

### Task-Oriented Datasets

| Dataset | Focus | Agentic Relevance |
|---------|-------|-------------------|
| **LawFlow** | [Paper](https://huggingface.co/papers/2504.18942) | End-to-end lawyer workflows | **Perfect for S1-S2** — captures iterative, multi-step reasoning |
| **LegalBench** | [nguha/legalbench](https://huggingface.co/datasets/nguha/legalbench) | 162 tasks, 6 reasoning types | Multi-hop reasoning tasks |
| **CLERC** | [GitHub](https://github.com/abehou/CLERC) | Citation retrieval + RAG | Retrieval + generation pipeline |
| **Lawyer-Instruct** | [Alignment-Lab-AI/Lawyer-Instruct](https://huggingface.co/datasets/Alignment-Lab-AI/Lawyer-Instruct) | Legal dialogue (instruction format) | Conversational legal tasks |

### Priority Datasets for S1-S2

| Priority | Dataset | Why |
|----------|---------|-----|
| 1 | **LawFlow** | Full lawyer workflow: client interview → research → drafting |
| 2 | **LegalBench** | 162 reasoning tasks, chainable into multi-hop |
| 3 | **CLERC** | Citation retrieval + generation for research planning |
| 4 | **Caselaw Access Project** | 6.7M cases for retrieval corpus |
| 5 | **CUAD** | Contract analysis for transactional workflows |

---

## Proposed English Agentic Framework

```
Legal-10 Agentic (English)
├── Corpora (adapt from HF)
│   ├── Caselaw Access Project (case retrieval)
│   ├── Pile of Law - statutes (statute lookup)
│   ├── CUAD (contract DB)
│   └── CLERC passages (citation retrieval)
├── Tools (build for English legal DBs)
│   ├── Case search (Westlaw/CourtListener API pattern)
│   ├── Statute lookup
│   ├── Citation validator
│   ├── Contract clause extractor
│   └── Math/aggregation tools
├── Tasks (300, adapted from)
│   ├── LawFlow workflows → multi-hop
│   ├── LegalBench reasoning → subtasks
│   └── CLERC retrieval → citation tasks
└── Evaluation
    ├── Success rate
    ├── Progress rate (intermediate keywords)
    └── BERT-Score
```

---

## Existing Legal AG Benchmarks (Reference)

### LegalAgentBench (ACL 2025)

- **Focus:** LLM Agents in Chinese legal domain
- **Dataset:** 17 corpora, 37 tools, 300 tasks
- **Paper:** [arxiv.org/abs/2412.17259](https://arxiv.org/abs/2412.17259)
- **GitHub:** [CSHaitao/LegalAgentBench](https://github.com/CSHaitao/LegalAgentBench)

### LegalBench (2023)

- **Focus:** Legal reasoning evaluation (not agentic)
- **Tasks:** 162 tasks, 6 reasoning categories
- **Paper:** [arxiv.org/abs/2308.11462](https://arxiv.org/abs/2308.11462)
- **GitHub:** [HazyResearch/legalbench](https://github.com/HazyResearch/legalbench)

### VLAIR (2025)

- **Focus:** Commercial legal AI tool evaluation
- **Tasks:** 7 task types (Document Q&A, Redlining, etc.)
- **Source:** [vals.ai/vlair](https://www.vals.ai/vlair)

### Harvey BigLaw Bench (2024)

- **Focus:** Real BigLaw tasks from billable time entries
- **Evaluation:** Rubric penalties for hallucinations, tone, relevance
- **Source:** [Legal IT Insider](https://legaltechnology.com/2024/09/03/the-gen-ai-llm-benchmarking-war-starts-here-harvey-releases-new-evaluation-framework/)

---

## General Agentic Benchmarks (Non-Legal Reference)

| Benchmark | Focus | Relevance |
|-----------|-------|-----------|
| [BFCL](https://gorilla.cs.berkeley.edu/leaderboard.html) | Function calling accuracy | Tool-use evaluation pattern |
| [AgentBench](https://github.com/THUDM/AgentBench) | LLM-as-agent across 8 environments | Multi-turn architecture |
| [SWE-bench](https://arxiv.org/abs/2310.06770) | Real GitHub issue resolution | Task completion pattern |
| [LiveSWEBench](https://github.com/livebench/liveswebench) | Tiered autonomy levels | Agentic vs prompted vs autocomplete |
| [DA-Code](https://arxiv.org/abs/2410.07331) | Data science agent tasks | Domain-specific agent pattern |
| [MLE-Bench](https://arxiv.org/abs/2410.07095) | ML engineering (Kaggle) | Professional task benchmark |

---

## LawFlow Dataset Details

**Paper:** [arxiv.org/abs/2504.18942](https://arxiv.org/abs/2504.18942)

### What It Captures

- End-to-end legal workflows from trained law students
- Business entity formation scenarios
- Dynamic, modular, iterative reasoning (not linear chains)

### Workflow Components

1. Client information elicitation
2. Issue identification
3. Note-taking
4. Legal research
5. Template selection
6. Drafting

### Key Finding

> Human workflows demonstrate modularity and adaptability. LLM workflows tend toward sequential, exhaustive processing with limited sensitivity to downstream implications.

### AI Role Insight

> Legal professionals envision AI excelling in supportive capacities: "brainstorming, identifying blind spots, and surfacing alternatives" — not executing entire workflows independently.

---

## Next Steps

1. [ ] Download and analyze LawFlow dataset structure
2. [ ] Map LegalBench tasks to multi-hop chains
3. [ ] Design English tool taxonomy (37 tools equivalent)
4. [ ] Identify corpora for each tool category
5. [ ] Create task annotation guidelines
6. [ ] Recruit legal expert annotators

---

## References

- Dahl, M., et al. (2024). Large Legal Fictions. *Journal of Legal Analysis*.
- LegalAgentBench (2025). ACL 2025. [arXiv:2412.17259](https://arxiv.org/abs/2412.17259)
- LegalBench (2023). [arXiv:2308.11462](https://arxiv.org/abs/2308.11462)
- LawFlow (2025). [arXiv:2504.18942](https://arxiv.org/abs/2504.18942)
- CLERC (2025). NAACL 2025. [arXiv:2406.17186](https://arxiv.org/abs/2406.17186)
