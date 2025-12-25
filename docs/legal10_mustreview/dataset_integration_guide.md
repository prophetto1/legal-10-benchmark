# HELM Dataset Integration Guide

## Overview

This guide catalogs how HELM connects to external datasets and provides patterns for integrating Legal-10 datasets.

---

## Dataset Sources

HELM supports multiple data source types:

| Source Type | Usage | Examples |
|-------------|-------|----------|
| **HuggingFace Datasets** | Primary method (~90% of scenarios) | CaseHOLD, LegalBench, LexGLUE |
| **Direct URLs** | Manual downloads with caching | Legal Support |
| **Git Repositories** | Version-controlled data | LegalBench prompt settings |
| **Local Files** | Post-download parsing | JSONL, CSV, TSV files |

---

## Current Legal Datasets in HELM

### 1. CaseHOLD (Case Holdings On Legal Decisions)

**File:** `src/helm/benchmark/scenarios/casehold_scenario.py`

**Dataset Source:**
```python
from datasets import load_dataset

dataset = load_dataset(
    "casehold/casehold",  # HuggingFace dataset ID
    "all",                 # Configuration/subset
    cache_dir=data_path   # Local cache: {output_path}/data
)
```

**HuggingFace:** [casehold/casehold](https://huggingface.co/datasets/casehold/casehold)

**Data Structure:**
- **Task:** Multiple choice QA (5 options)
- **Splits:** `train`, `test`
- **Fields:**
  - `example_id` - Unique identifier
  - `citing_prompt` - Context passage
  - `holding_0` through `holding_4` - Answer choices
  - `label` - Correct answer index ("0"-"4")

**Run Spec:** `enterprise_run_specs.py::get_casehold_spec()`

**Adapter:** Multiple Choice Joint

**Metrics:** Exact Match

---

### 2. LegalBench

**File:** `src/helm/benchmark/scenarios/legalbench_scenario.py`

**Dataset Source:**
```python
import datasets

train_dataset = datasets.load_dataset(
    "nguha/legalbench",
    subset,  # e.g., "abercrombie", "corporate_lobbying"
    trust_remote_code=True,
    cache_dir=cache_dir,
    split="train",
    revision="e042ea68c19df12b737fe768572f22ead61e8e37"  # Pinned version
)
```

**HuggingFace:** [nguha/legalbench](https://huggingface.co/datasets/nguha/legalbench)

**Supported Subsets:**
- `abercrombie` - Trademark classification
- `corporate_lobbying` - Corporate disclosure analysis
- `international_citizenship_questions` - Citizenship law QA
- `function_of_decision_section` - Legal document structure
- `proa` - Legal reasoning

**Additional Resources:**
```python
# External prompt configuration
PROMPT_SETTINGS_URL = "https://raw.githubusercontent.com/HazyResearch/legalbench/main/helm_prompt_settings.jsonl"

ensure_file_downloaded(
    source_url=PROMPT_SETTINGS_URL,
    target_path=prompt_settings_path
)
```

**Run Spec:** `lite_run_specs.py::get_legalbench_spec(subset)`

**Key Feature:** Uses `revision` parameter for reproducibility

---

### 3. LexGLUE (Legal General Language Understanding)

**File:** `src/helm/benchmark/scenarios/lex_glue_scenario.py`

**Dataset Source:**
```python
dataset = load_dataset(
    "lex_glue",
    config,  # Task config: "ecthr_a", "scotus", "case_hold", etc.
    cache_dir=cache_dir
)
```

**HuggingFace:** [lex_glue](https://huggingface.co/datasets/lex_glue)

**Supported Tasks:**

| Config | Task Type | Description |
|--------|-----------|-------------|
| `ecthr_a` | Multi-label | European Court of Human Rights (Articles) |
| `ecthr_b` | Multi-label | European Court of Human Rights (Violations) |
| `scotus` | Single-label | US Supreme Court topic classification |
| `eurlex` | Multi-label | EU legislation classification |
| `ledgar` | Single-label | Legal contract provisions |
| `unfair_tos` | Multi-label | Terms of Service unfairness detection |
| `case_hold` | QA | Legal case holding identification |

**Run Spec:** `classic_run_specs.py::get_lex_glue_spec(subset)`

**Dynamic Configuration:**
- Task type determines adapter (generation vs classification)
- Max tokens varies by task (20-100)
- Max train instances varies (0-5)

---

### 4. Legal Summarization (Multi-Dataset)

**File:** `src/helm/benchmark/scenarios/legal_summarization_scenario.py`

**Supports 3 Datasets:**

#### A. BillSum (US Congressional Bills)
```python
dataset = load_dataset("billsum", cache_dir=cache_dir)
# Fields: text, summary
```

**HuggingFace:** [billsum](https://huggingface.co/datasets/billsum)

**Run Spec:** `classic_run_specs.py::get_billsum_legal_summarization_spec()`

**Summary Length:** 200-800 tokens

#### B. MultiLexSum (Legal Case Summaries)
```python
dataset = load_dataset(
    "allenai/multi_lexsum",
    "v20220616",  # Versioned config
    cache_dir=cache_dir
)
# Fields: summary/long, summary/short
```

**HuggingFace:** [allenai/multi_lexsum](https://huggingface.co/datasets/allenai/multi_lexsum)

**Run Spec:** `classic_run_specs.py::get_multilexsum_legal_summarization_spec()`

**Summary Length:** 100-400 tokens

#### C. EurLexSum (EU Legislation)
```python
dataset = load_dataset(
    "dennlinger/eur-lex-sum",
    "english",  # Language config
    cache_dir=cache_dir
)
# Fields: reference, summary
```

**HuggingFace:** [dennlinger/eur-lex-sum](https://huggingface.co/datasets/dennlinger/eur-lex-sum)

**Run Spec:** `classic_run_specs.py::get_eurlexsum_legal_summarization_spec()`

**Summary Length:** 400-1600 tokens

---

### 5. Legal Support (Argumentative Reasoning)

**File:** `src/helm/benchmark/scenarios/legal_support_scenario.py`

**Dataset Source:** Direct URL Download
```python
from helm.common.general import ensure_file_downloaded

ensure_file_downloaded(
    source_url="https://docs.google.com/uc?export=download&id=1PVoyddrCHChMxYrLhsI-zu7Xzs5S8N77",
    target_path=data_path,
    unpack=True,
    unpack_type="unzip"
)

# Reads local JSONL files:
# - train.jsonl
# - dev.jsonl
# - test.jsonl
```

**Data Structure:**
```json
{
  "context": "Legal passage text...",
  "citation_a": "First citation with (parenthetical text)",
  "citation_b": "Second citation with (parenthetical text)",
  "label": "a"  // or "b"
}
```

**Run Spec:** `classic_run_specs.py::get_legal_support_spec()`

**Task:** Binary multiple choice (which citation better supports the passage)

---

### 6. LEXtreme (Multilingual Legal NLU)

**File:** `src/helm/benchmark/scenarios/lextreme_scenario.py`

**Dataset Source:**
```python
dataset = load_dataset(
    "joelito/lextreme",
    subset,  # 18 different legal tasks
    cache_dir=cache_dir
)
```

**HuggingFace:** [joelito/lextreme](https://huggingface.co/datasets/joelito/lextreme)

**18 Legal Tasks** across multiple languages (Portuguese, German, Greek, French, Italian, Romanian)

---

## Standard Dataset Integration Patterns

### Pattern 1: HuggingFace Datasets (Recommended)

**Use When:** Dataset is publicly available on HuggingFace Hub

**Template:**
```python
from typing import List
import os
from datasets import load_dataset, DatasetDict
from helm.benchmark.scenarios.scenario import (
    Scenario, Instance, Input, Output, Reference,
    TRAIN_SPLIT, TEST_SPLIT, CORRECT_TAG
)
from helm.common.general import ensure_directory_exists

class Legal10ExampleScenario(Scenario):
    name = "legal_10_example"
    description = "Legal-10 example dataset"
    tags = ["legal", "legal_10"]

    def get_instances(self, output_path: str) -> List[Instance]:
        # Step 1: Set up cache directory
        cache_dir = os.path.join(output_path, "data")
        ensure_directory_exists(cache_dir)

        # Step 2: Load dataset from HuggingFace
        dataset = load_dataset(
            "legal-10/example-dataset",  # HuggingFace dataset ID
            "default",                    # Config/subset (optional)
            cache_dir=cache_dir,
            revision="main"               # Or specific commit hash
        )

        # Step 3: Convert to HELM instances
        instances = []
        for split_name in ["train", "test"]:
            helm_split = TRAIN_SPLIT if split_name == "train" else TEST_SPLIT

            for idx, example in enumerate(dataset[split_name]):
                instance = Instance(
                    input=Input(text=example["input_text"]),
                    references=[
                        Reference(
                            Output(text=example["answer"]),
                            tags=[CORRECT_TAG]
                        )
                    ],
                    split=helm_split,
                    id=f"{split_name}_{idx}"
                )
                instances.append(instance)

        return instances
```

**Advantages:**
- Automatic caching
- Version control via `revision` parameter
- Standardized interface
- Easy data exploration on HuggingFace website

---

### Pattern 2: Direct URL Download

**Use When:** Dataset is hosted externally (not on HuggingFace)

**Template:**
```python
import json
from helm.common.general import ensure_file_downloaded

class Legal10URLScenario(Scenario):
    name = "legal_10_url_example"
    description = "Dataset from external URL"
    tags = ["legal", "legal_10"]

    DATASET_URL = "https://example.com/dataset.zip"

    def get_instances(self, output_path: str) -> List[Instance]:
        # Step 1: Download and unpack
        data_path = os.path.join(output_path, "data")
        ensure_file_downloaded(
            source_url=self.DATASET_URL,
            target_path=data_path,
            unpack=True,
            unpack_type="zip"  # or "tar", "unzstd"
        )

        # Step 2: Read local files
        instances = []
        with open(os.path.join(data_path, "train.jsonl")) as f:
            for line in f:
                data = json.loads(line)
                instance = Instance(
                    input=Input(text=data["question"]),
                    references=[
                        Reference(Output(text=data["answer"]), tags=[CORRECT_TAG])
                    ],
                    split=TRAIN_SPLIT,
                    id=data["id"]
                )
                instances.append(instance)

        return instances
```

**Advantages:**
- Works with any HTTP/HTTPS URL
- Automatic unpacking (zip, tar, tar.gz, zstd)
- Local file caching

---

### Pattern 3: Multiple Choice with References

**Use When:** Dataset has multiple answer choices (like CaseHOLD)

**Template:**
```python
class Legal10MultipleChoiceScenario(Scenario):
    name = "legal_10_mcqa"
    description = "Multiple choice QA"
    tags = ["legal", "question_answering", "legal_10"]

    NUM_CHOICES = 4  # Number of answer options

    def get_instances(self, output_path: str) -> List[Instance]:
        cache_dir = os.path.join(output_path, "data")
        ensure_directory_exists(cache_dir)

        dataset = load_dataset("legal-10/mcqa", cache_dir=cache_dir)

        instances = []
        for example in dataset["test"]:
            # Extract answer choices
            choices = [
                example["choice_a"],
                example["choice_b"],
                example["choice_c"],
                example["choice_d"]
            ]
            correct_idx = int(example["label"])  # 0-3

            # Create references (one per choice)
            references = [
                Reference(
                    Output(text=choices[i]),
                    tags=[CORRECT_TAG] if i == correct_idx else []
                )
                for i in range(self.NUM_CHOICES)
            ]

            instance = Instance(
                input=Input(text=example["question"]),
                references=references,
                split=TEST_SPLIT,
                id=example["id"]
            )
            instances.append(instance)

        return instances
```

**Key Points:**
- One `Reference` per answer choice
- Only correct answer has `CORRECT_TAG`
- Used with `ADAPT_MULTIPLE_CHOICE_JOINT` adapter

---

## Cache Directory Structure

HELM uses a consistent caching pattern:

```
benchmark_output/
└── scenarios/
    └── legal_10_example/
        ├── data/                    # Dataset cache (HuggingFace or downloaded)
        │   ├── downloads/          # Raw downloads
        │   └── legal-10___example/ # Processed HuggingFace cache
        ├── instances.json          # Generated instances
        └── scenario_state.json     # Execution results
```

**Key Functions:**
```python
from helm.common.general import (
    ensure_directory_exists,      # Create dir if not exists
    ensure_file_downloaded,       # Download file with caching
)
from helm.benchmark.scenarios.scenario import get_scenario_cache_path

# Get standard cache path
cache_path = get_scenario_cache_path(benchmark_output_path, "legal_10_example")
# Returns: benchmark_output/scenarios/legal_10_example
```

---

## Reproducibility Best Practices

### 1. Pin Dataset Versions

**HuggingFace:**
```python
dataset = load_dataset(
    "legal-10/dataset",
    revision="abc123def456"  # Specific git commit hash
)
```

**Direct URLs:**
```python
# Include version in filename or URL
DATASET_URL = "https://example.com/dataset-v1.2.3.zip"
```

### 2. Document Data Source

```python
class Legal10Scenario(Scenario):
    """
    Legal-10 Example Dataset

    Dataset repository: https://huggingface.co/datasets/legal-10/example
    Publication: Smith et al. (2024). "Legal AI Benchmark." ICAIL.

    Data content:
      The dataset consists of X legal questions with Y answer choices.
      Questions are derived from Z legal domain.
    """
    name = "legal_10_example"
```

### 3. Handle Missing Data Gracefully

```python
def get_instances(self, output_path: str) -> List[Instance]:
    try:
        dataset = load_dataset("legal-10/dataset", cache_dir=cache_dir)
    except Exception as e:
        raise ValueError(
            f"Failed to load dataset 'legal-10/dataset'. "
            f"Ensure you have internet connection and HuggingFace access. Error: {e}"
        )
```

---

## Legal-10 Dataset Requirements

For Legal-10 benchmark integration, each skill needs:

### Skill Datasets

| Skill | Dataset | Preferred Source | Status |
|-------|---------|------------------|--------|
| S1-S2 | LegalAgentBench | HuggingFace or URL | To be uploaded |
| S3-S4 | CLERC | HuggingFace or URL | To be uploaded |
| S5 | KeyCite-CLERC | HuggingFace or URL | To be uploaded |
| S6 | CUAD | HuggingFace: `cuad` | Available |
| S7 | CaseHOLD | HuggingFace: `casehold/casehold` | ✅ Integrated |
| S8 | LEXam | HuggingFace or URL | To be uploaded |
| S9 | Dahl 10-types | HuggingFace or URL | To be uploaded |
| S10 | SHIELD | HuggingFace or URL | To be uploaded |

### Dataset Upload Checklist

For HuggingFace hosting:

- [ ] Create HuggingFace dataset repository
- [ ] Upload data files (train/test/validation splits)
- [ ] Write dataset card (README.md) with:
  - [ ] Description and citation
  - [ ] Data structure documentation
  - [ ] Field descriptions
  - [ ] Licensing information
- [ ] Add dataset loading script (if custom format)
- [ ] Test loading: `load_dataset("legal-10/dataset-name")`
- [ ] Pin revision for reproducibility

---

## Testing Dataset Integration

### 1. Test Loading
```python
# Test in Python REPL
from datasets import load_dataset

dataset = load_dataset("legal-10/example")
print(dataset)
print(dataset["train"][0])  # First example
```

### 2. Test Scenario
```python
# Run scenario in isolation
from helm.benchmark.scenarios.legal_10_example_scenario import Legal10ExampleScenario

scenario = Legal10ExampleScenario()
instances = scenario.get_instances("test_output")
print(f"Loaded {len(instances)} instances")
print(instances[0])
```

### 3. Test Run Spec
```bash
# Run small evaluation
helm-run \
  --run-specs legal_10_example \
  --max-eval-instances 5 \
  --output-path test_output
```

---

## Common Issues & Solutions

### Issue 1: Dataset Not Found

**Error:** `FileNotFoundError` or `DatasetNotFoundError`

**Solutions:**
- Verify dataset ID is correct
- Check HuggingFace dataset is public
- Ensure internet connection
- Try with `trust_remote_code=True` if dataset has custom script

### Issue 2: Cache Permission Errors

**Error:** `PermissionError` when writing to cache

**Solutions:**
- Ensure `output_path` directory is writable
- Check disk space
- Use `ensure_directory_exists()` before writing

### Issue 3: Version Mismatch

**Error:** Dataset structure changed from expected format

**Solutions:**
- Pin `revision` parameter to specific commit
- Document expected dataset version in scenario docstring
- Add validation in `get_instances()`

---

## Summary

HELM's dataset integration follows a **consistent, modular pattern**:

1. **Scenarios load data** in `get_instances()` method
2. **HuggingFace is preferred** for standardization and caching
3. **Direct URLs work** for externally hosted datasets
4. **Caching is automatic** in `{output_path}/data/` directory
5. **Reproducibility** via `revision` pinning and version documentation

For Legal-10:
- Upload datasets to HuggingFace under `legal-10/` organization
- Follow existing legal scenario patterns (CaseHOLD, LegalBench)
- Use `revision` parameter for all datasets
- Document data sources and citations clearly