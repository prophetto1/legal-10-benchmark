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

| Skill | Name | Modality | Dataset |
|-------|------|----------|---------|
| **S1** | Research Planning | AG | LegalAgentBench |
| **S2** | Strategic Stopping | AG | LegalAgentBench |
| **S3** | Known Authority | RAG | CLERC |
| **S4** | Unknown Authority | RAG | CLERC (semantic) |
| **S5** | Validate Authority | RAG | KeyCite-CLERC |
| **S6** | Fact Extraction | RAG | CUAD |
| **S7** | Distinguish Cases | CB | CaseHOLD |
| **S8** | Synthesize Results | CB | LEXam |
| **S9** | Citation Integrity | CB | Dahl 10-types |
| **S10** | Copyright Compliance | CB | SHIELD |

### Extensions

| # | Name | Modality | Dataset |
|---|------|----------|---------|
| **1** | Multilingual Reasoning | CB | FairLex |

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

## Architecture

Legal-10 implements the evaluation architecture pioneered by Stanford CRFM's HELM (Holistic Evaluation of Language Models). We chose this foundation because it embodies the values we believe legal AI evaluation requires:

- Unified model interface — open-weight + closed API
- Standardized adapters — same prompts for all models
- Reproducible evaluation — auditable run bundles
- Transparent metrics — open-source scoring

## Community-Driven

Legal-10 is community-governed, not vendor-controlled. We ask for your help in pushing this benchmark initiative. We need a fair and comprehensive benchmark that end users can rely on to better understand the quality implications of a product they are using or considering.

**Benefits:** Co-author credit on papers, governance voting rights, contributor recognition