# Legal-10 Benchmark

Legal-10 is an open, skill-based benchmark suite designed to make legal AI performance inspectable, comparable, and auditable—across three delivery modalities: Closed-Book (CB), Retrieval-Augmented Generation (RAG), and Agentic Workflows (AG).

We open-source everything: datasets, harness, scoring, and operational tooling, while ensuring we protect test We will release benchmark. We support evaluation of both open-weight and closed API models via a reproducible run-bundle protocol.

## Key Principle

**Grounded in Professional Standards.** 

- MacCrate Report (ABA, 1992) — foundational lawyering skills
- AALL Principles & Standards (2013) — legal research competency standards
- Shultz & Zedeck (2011) — empirically-derived lawyer effectiveness factors

Every skill maps to reputable frameworks the legal profession has validated over 30+ years. These standards are used to translate human-lawyer competencies into 10 minimal viable tests across distinct cognitive circuits.

Testing conducted using three modalities of Agentic Workflow (AG), RAG, or Closed Book (CB). Each skill is tested in the modality that most closely matches how human lawyers perform that skill in computer-mediated work.

## The 10 Skills

| Skill | Name | Modality | Dataset |
|-------|------|----------|---------|
| **S1** | Research Planning | AG | L10 AG |
| **S2** | Strategic Stopping | AG | L10 AG |
| **S3** | Known Authority | RAG | CLERC |
| **S4** | Unknown Authority | RAG | CLERC |
| **S5** | Validate Authority | RAG | CLERC |
| **S6** | Fact Extraction | RAG | CUAD |
| **S7** | Distinguish Cases | CB | CaseHOLD |
| **S8** | Synthesize Results | CB | LEXam |
| **S9** | Citation Integrity | CB |  |
| **S10** | Copyright Compliance | CB | SHIELD |

### Extensions

| # | Name | Modality | Dataset |
|---|------|----------|---------|
| **1** | Multilingual Reasoning | CB | FairLex |

## Transparent, Community-Driven

- open-source harness, datasets, assessment criteria
- Auditable run bundles, SHA-256 hashes, signed manifests
- Maintain transparent coverage disclosure, governance
- Public append-only log — all submissions preserved

## Objective, Informative

- ensure understanding of quality implications of products being used by industry end users.
- ensure evaluations are controlled so reports are objective and purposeful
- Built using Stanford CFRM HELM (Holistic Evaluation of Language Models) Architecture

## Scoreboard Policy

- No one games system. Make it transparently clear. 
- No selective omission or quiet withdrawal → if 10, then run all 10. 
- Skill-level reporting → system prevents unsatisfactory areas or skills from being hidden 
- system prevents failures from being hidden 