# Legal-10 Benchmark

Legal AI is already being used to make decisions that affect liberty, employment, housing, immigration status, family stability, and access to counsel. Today's evaluation ecosystem is fragmented, vendor-permissioned, and often optimized for optics rather than end-user protection.

Legal-10 is an open, skill-based benchmark suite designed to make legal AI performance inspectable, comparable, and auditable—across three delivery modalities: Closed-Book (CB), Retrieval-Augmented Generation (RAG), and Agentic Workflows (AG).

We open-source everything: datasets, harness, scoring, and operational tooling. The only exception is the actual administered test data, which remains private to prevent contamination and prior knowledge. We support evaluation of both open-weight and closed API models via a reproducible run-bundle protocol.

## Key Principle

**Grounded in Professional Standards.** Legal skills are not one task.

- MacCrate Report (ABA, 1992) — foundational lawyering skills
- AALL Principles & Standards (2013) — legal research competency standards
- Shultz & Zedeck (2011) — empirically-derived lawyer effectiveness factors

Every skill maps to reputable frameworks the legal profession has validated over 30+ years. These standards are used to translate human-lawyer competencies into 10 minimal viable tests across distinct cognitive circuits.

Testing conducted using three modalities of Agentic Workflow (AG), RAG, or Closed Book (CB). Each skill is tested in the modality that most closely matches how human lawyers perform that skill in computer-mediated work.

## The 10 Skills

| Skill | Name | Dataset | Description | Modality |
|-------|------|---------|-------------|----------|
| **S1** | Research Planning | LegalAgentBench | Plan and execute multi-step research strategy under budget with clear stop rule | Agentic Workflows (AG) |
| **S2** | Strategic Stopping | LegalAgentBench | Terminate research at the right time given diminishing returns and uncertainty | Agentic Workflows (AG) |
| **S3** | Known Authority | CLERC | Resolve a known citation/identifier to the correct authority quickly and exactly | Retrieval-Augmented Generation (RAG) |
| **S4** | Unknown Authority | CLERC (semantic) | Retrieve relevant law from a fact pattern and rank/select the best authority | Retrieval-Augmented Generation (RAG) |
| **S5** | Validate Authority | KeyCite-CLERC | Determine whether authority remains good law and summarize negative treatment | Retrieval-Augmented Generation (RAG) |
| **S6** | Fact Extraction | CUAD | Extract specific facts from long contracts (needle-in-haystack retrieval) | Retrieval-Augmented Generation (RAG) |
| **S7** | Distinguish Cases | CaseHOLD | Decide whether a case applies or can be distinguished based on facts/reasoning | Closed-Book (CB) |
| **S8** | Synthesize Results | LEXam | Combine multiple authorities into a cohesive principle, memo, or argument | Closed-Book (CB) |
| **S9** | Citation Integrity | Dahl 10-types | Avoid fabricated authority; ensure grounding and adverse authority obligations | Closed-Book (CB) |
| **S10** | Copyright Compliance | SHIELD | Refuse protected content and minimize verbatim memorization | Closed-Book (CB) |

### Extensions

| # | Name | Dataset | Description |
|---|------|---------|-------------|
| **1** | Multilingual Reasoning | FairLex | Evaluate legal reasoning across jurisdictions/languages; test quantization transparency |

## Design Principles

### Objectivity
- Fixed spec per version — changes bump the version
- No selective omission or quiet withdrawal — full eval split or no score

### Transparency
- Open-source harness — datasets, scoring, tools
- Auditable run bundles — SHA-256 hashes, signed manifests
- Public append-only log — all submissions preserved

### Fairness
- Comparable baselines — open and closed models use identical harness
- Modality-matched testing — CB vs RAG vs AG reflects real lawyer workflows

### Gaming-Resistant
- No selective omission → must run all 10 skills
- Skill-level reporting → can't hide weak skills in aggregate
- Visible failure modes → can't hide how it failed

## Scoreboard Policy

Participation is your choice. We will:

1. List all evaluated systems with linked run bundles ("Evaluated")
2. Maintain a neutral "Not Yet Evaluated" coverage list

**Coverage disclosure.** Each "Not Yet Evaluated" entry includes a neutral status tag (e.g., "no public endpoint," "access restricted," "awaiting community submission").

This ensures: Fair comparison across GPT-4, Claude, Llama, and other models using identical evaluation protocols.

## Development Architecture - HELM

Legal-10 uses Stanford CRFM's Holistic Evaluation of Language Models (HELM) framework because it embodies many of the values we strive to establish moving forward:

- Unified model interface — open-weight + closed API
- Standardized adapters — same prompts for all models
- Reproducible evaluation — auditable run bundles
- Transparent metrics — open-source scoring

## Community-Driven

Legal-10 is community-governed, not vendor-controlled. We ask for your help in pushing this benchmark initiative. We need a fair and comprehensive benchmark that end users can rely on to better understand the quality implications of a product they are using or considering.

**Benefits:** Co-author credit on papers, governance voting rights, contributor recognition