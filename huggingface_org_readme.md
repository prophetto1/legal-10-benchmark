# Legal-10 Benchmark

**An open, skill-based benchmark suite for evaluating legal AI systems**

---

Legal AI is already being used to make decisions that affect liberty, employment, housing, immigration status, family stability, and access to counsel. Today's evaluation ecosystem is fragmented, vendor-permissioned, and often optimized for optics rather than end-user protection.

Legal-10 makes legal AI performance **inspectable**, **comparable**, and **auditable** across three delivery modalities: Closed-Book (CB), Retrieval-Augmented Generation (RAG), and Agentic Workflows (AG).

## Key Principle

**Grounded in Professional Standards.** Legal skills are not one task.

Every skill maps to reputable frameworks the legal profession has validated over 30+ years:

- **MacCrate Report** (ABA, 1992) — foundational lawyering skills
- **AALL Principles & Standards** (2013) — legal research competency standards
- **Shultz & Zedeck** (2011) — empirically-derived lawyer effectiveness factors

## The 10 Skills

| Skill | Name | Modality |
|-------|------|----------|
| **S1** | Research Planning | Agentic |
| **S2** | Strategic Stopping | Agentic |
| **S3** | Known Authority | RAG |
| **S4** | Unknown Authority | RAG |
| **S5** | Validate Authority | RAG |
| **S6** | Fact Extraction | RAG |
| **S7** | Distinguish Cases | Closed-Book |
| **S8** | Synthesize Results | Closed-Book |
| **S9** | Citation Integrity | Closed-Book |
| **S10** | Copyright Compliance | Closed-Book |

### Skill Definitions

- **S1 Research Planning** — Constructing a systematic research strategy that identifies relevant legal issues, selects appropriate sources, and sequences tasks efficiently
- **S2 Strategic Stopping** — Knowing when sufficient research has been conducted vs. diminishing returns
- **S3 Known Authority** — Precise retrieval of specific legal authority when the citation is already known
- **S4 Unknown Authority** — Discovering relevant legal authority iteratively when the citation is unknown
- **S5 Validate Authority** — Verifying that legal authority remains valid via subsequent treatment and amendments
- **S6 Fact Extraction** — Identifying and extracting legally relevant facts from source documents
- **S7 Distinguish Cases** — Analyzing whether precedent applies by comparing facts, issues, and reasoning
- **S8 Synthesize Results** — Integrating holdings from multiple authorities into coherent legal principles
- **S9 Citation Integrity** — Ensuring citations exist, accurately support propositions, and avoid fabrication
- **S10 Copyright Compliance** — Refusing verbatim reproduction of copyrighted content

## Design Principles

### Objectivity
- Fixed specification per version — changes bump the version
- All 10 skills must be evaluated — no partial scores or selective omission

### Transparency
- Open-source harness, datasets, and scoring
- Auditable run bundles with SHA-256 hashes and signed manifests
- Public append-only log — all submissions preserved

### Fairness
- Identical evaluation protocol for all models
- Same prompts, datasets, and metrics across open-weight and closed API models
- Modality-matched testing reflects real lawyer workflows

### Gaming Resistance
- Must run all 10 skills — no cherry-picking favorable tasks
- Skill-level reporting — can't hide weak skills in aggregate
- Visible failure modes — can't hide how models failed

## Architecture

Legal-10 implements the evaluation architecture pioneered by Stanford CRFM's **HELM** (Holistic Evaluation of Language Models):

- **Unified model interface** — open-weight + closed API
- **Standardized adapters** — same prompts for all models
- **Reproducible evaluation** — auditable run bundles
- **Transparent metrics** — open-source scoring

## Scoreboard Policy

1. **"Evaluated" list** — All systems with complete evaluation (all 10 skills) linked to run bundles
2. **"Not Yet Evaluated" list** — Neutral status tags (e.g., "no public endpoint," "access restricted," "awaiting community submission")

This ensures fair comparison across GPT-4, Claude, Llama, and other models using identical evaluation protocols.

## What Legal-10 Measures

- Behavioral performance on standardized legal tasks
- Skill-specific capabilities across 10 professional competency areas
- Comparative capabilities under identical conditions

## What Legal-10 Does Not Measure

- Model size, precision, or quantization
- Training data composition
- Production reliability or edge cases
- Fitness for specific jurisdictions or use cases

## Community-Driven

Legal-10 is community-governed, not vendor-controlled.

**Benefits for contributors:**
- Co-author credit on papers
- Governance voting rights
- Contributor recognition

## Links

- **Documentation:** [legal-10-benchmark](https://prophetto1.github.io/legal-10-benchmark/)
- **GitHub:** [github.com/prophetto1/legal-10-benchmark](https://github.com/prophetto1/legal-10-benchmark)

## Citation

```bibtex
@misc{legal10_2025,
  title={Legal-10: A Skill-Based Benchmark for Legal AI Evaluation},
  author={Chung, Jon},
  year={2025},
  url={https://github.com/prophetto1/legal-10-benchmark}
}
```

---

*Legal-10 is an open benchmark. We welcome scrutiny, replication, and improvement suggestions.*