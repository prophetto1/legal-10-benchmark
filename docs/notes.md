# Legal-10 Development Notes

## Dataset Availability Status

### ✓ Available on HuggingFace
- **CUAD** - [theatticusproject/cuad-qa](https://huggingface.co/datasets/theatticusproject/cuad-qa) (S6: Fact Extraction)
- **CLERC** - [jhu-clsp/CLERC](https://huggingface.co/datasets/jhu-clsp/CLERC) (S3, S4, S5: Citation/Authority tasks)
- **LEXam** - [LEXam-Benchmark/LEXam](https://huggingface.co/datasets/LEXam-Benchmark/LEXam) (S8: Synthesize Results)
- **FairLex** - [coastalcph/fairlex](https://huggingface.co/datasets/coastalcph/fairlex) (Extension: Multilingual)
- **CaseHOLD** - [casehold/casehold](https://huggingface.co/datasets/casehold/casehold) (S7: Distinguish Cases) - Already in HELM

### ⚠ Available on GitHub
- **Dahl** - [reglab/legal_hallucinations](https://github.com/reglab/legal_hallucinations) (S9: Citation Integrity)
- **LegalAgentBench** - [CSHaitao/LegalAgentBench](https://github.com/CSHaitao/LegalAgentBench) (S1, S2: Agentic) - Chinese-focused

### ❌ Not Publicly Available
- **SHIELD** - Dataset not public due to copyright concerns (S10: Copyright Compliance)
  - Paper: [arXiv:2406.12975](https://arxiv.org/abs/2406.12975)

## Existing HELM Implementations

### Already Implemented
- **CaseHOLD** - `src/helm/benchmark/scenarios/casehold_scenario.py`
  - ~53k questions, 5-choice MC
  - Run spec: `enterprise_run_specs.py::get_casehold_spec()`

- **LegalBench** - `src/helm/benchmark/scenarios/legalbench_scenario.py`
  - ~100 instances per subset (5 subsets)
  - Run spec: `lite_run_specs.py::get_legalbench_spec(subset)`

### To Implement (8 datasets)
1. CLERC (S3, S4, S5)
2. CUAD (S6)
3. LEXam (S8)
4. LegalAgentBench (S1, S2)
5. KeyCite (S5) - unclear if separate or part of CLERC
6. SHIELD (S10)
7. Dahl (S9)
8. FairLex (Extension)

## Implementation Patterns (from CaseHOLD & LegalBench)

### Dataset Loading
- Use HuggingFace `load_dataset()` with `cache_dir` parameter
- Cache to `output_path/data/`
- Support train/test splits

### Instance Structure
```python
Instance(
    input=Input(text="..."),
    references=[Reference(Output(text="..."), tags=["correct" or []])],
    split="train" or "test",
    id=f"id{unique_id}"
)
```

### Run Spec Pattern
- Few-shot: 2-5 examples
- Metrics: `get_exact_match_metric_specs()`
- Adapt methods: `ADAPT_MULTIPLE_CHOICE_JOINT` or `ADAPT_GENERATION`

## Computational Estimates

### Per-Dataset (based on CaseHOLD/LegalBench)
- **CaseHOLD**: ~1200-1850 tokens/instance, 2 few-shot examples
- **LegalBench**: ~650-1250 tokens/instance, 5 few-shot examples
- **Estimated runtime**: 30-60 min for 1 model across all 10 skills (~1000-1500 test instances)

## Branding Changes Completed
- [x] README.md updated with Legal-10 mission statement
- [x] CITATION.bib updated (author: Jon Chung)
- [ ] pyproject.toml (package name, description, author)
- [ ] LICENSE (copyright holder)
- [ ] Frontend branding

## Next Steps
1. Decide on test set sampling strategy (all datasets vary in size)
2. Create `legal10_run_specs.py` for all 10 skills
3. Implement missing scenario files
4. Handle SHIELD alternative (S10)
5. Handle LegalAgentBench language issue (S1, S2)
# HELM Framework Architectural Assessment for Legal-10 Integration

## Executive Summary

The HELM framework provides a **clean extension architecture** that allows Legal-10 to integrate completely without touching core framework code. The system uses:

- **Inheritance-based extension** (scenarios, metrics, adapters)
- **Decorator-based registration** (run specs via `@run_spec_function`)
- **Factory pattern instantiation** (scenarios, metrics via class names)
- **YAML-based configuration** (models, deployments)

---

## 1. Constitutional Boundaries - DO NOT TOUCH

These files form the backbone of HELM's execution engine and **must NOT be modified** when integrating Legal-10.

### Core Framework Files

| Category | Files | Purpose | Why Not to Touch |
|----------|-------|---------|------------------|
| **Base Classes** | `scenario.py`<br>`adapter.py`<br>`metric.py`<br>`run_spec.py` | Define contracts all extensions must follow | Frozen dataclasses and abstract methods form the interface contract |
| **Execution Engine** | `runner.py`<br>`executor.py`<br>`scenario_state.py`<br>`request_state.py` | Orchestrate benchmark execution pipeline | Changing flow logic breaks all existing benchmarks |
| **Registration** | `config_registry.py`<br>`model_metadata_registry.py`<br>`model_deployment_registry.py` | Discover and register components | Central registry used by entire framework |
| **Factory/Discovery** | `adapter_factory.py`<br>`run_spec_factory.py`<br>`object_spec.py` | Instantiate components from specs | Routing logic for all adapter/scenario creation |

### Specific Files (Absolute Paths)

**DO NOT MODIFY:**

- `/src/helm/benchmark/scenarios/scenario.py` - Base `Scenario` class
- `/src/helm/benchmark/run_spec.py` - `RunSpec` dataclass and decorator
- `/src/helm/benchmark/adaptation/adapter_spec.py` - `AdapterSpec` and constants
- `/src/helm/benchmark/adaptation/adapters/adapter.py` - Base `Adapter` class
- `/src/helm/benchmark/metrics/metric.py` - Base `Metric` class
- `/src/helm/benchmark/runner.py` - Execution orchestrator
- `/src/helm/benchmark/executor.py` - Request executor
- `/src/helm/benchmark/model_metadata_registry.py` - Model registry
- `/src/helm/benchmark/model_deployment_registry.py` - Deployment registry
- `/src/helm/benchmark/config_registry.py` - Configuration loader

**Key Principle:** Never modify frozen dataclasses, abstract methods, or execution flow logic.

---

## 2. Safe Extension Points - Legal-10 Implementation

These are the designed interfaces for adding Legal-10 content.

### What to CREATE (not modify)

```
src/helm/benchmark/
├── scenarios/
│   ├── legal_10_clerc_scenario.py          ← S3, S4: Known/Unknown Authority
│   ├── legal_10_keycite_scenario.py        ← S5: Validate Authority
│   ├── legal_10_cuad_scenario.py           ← S6: Fact Extraction
│   ├── legal_10_lexam_scenario.py          ← S8: Synthesize Results
│   ├── legal_10_citation_scenario.py       ← S9: Citation Integrity
│   ├── legal_10_shield_scenario.py         ← S10: Copyright Compliance
│   └── legal_10_agentbench_scenario.py     ← S1, S2: Research Planning/Stopping
│
├── run_specs/
│   └── legal_10_run_specs.py               ← All 10 @run_spec_function defs
│
└── metrics/
    └── legal_10_metrics.py                 ← Custom metrics if needed

docs/
├── legal_10_overview.md                    ← Benchmark introduction
├── legal_10_scenarios.md                   ← Scenario details
└── legal_10_setup.md                       ← Setup guide
```

### A. Adding Custom Scenarios

**Pattern:** Create Scenario subclass inheriting from `Scenario` ABC

**Location:** Create new files in `/src/helm/benchmark/scenarios/`

**Template:**

```python
from typing import List
from helm.benchmark.scenarios.scenario import (
    Scenario, Instance, Input, Output, Reference,
    CORRECT_TAG, TEST_SPLIT, TRAIN_SPLIT
)
from helm.common.general import ensure_directory_exists

class Legal10CLERCScenario(Scenario):
    """
    CLERC: Citation Resolution benchmark for Legal-10
    Tests known authority retrieval (S3)
    """
    name = "legal_10_clerc"
    description = "CLERC: Known Authority Retrieval"
    tags = ["legal", "retrieval", "legal_10"]

    def get_instances(self, output_path: str) -> List[Instance]:
        # Load CLERC dataset
        # Create Instance objects with Input and References
        instances = []

        # Example instance creation
        instance = Instance(
            input=Input(text="Find 42 U.S.C. § 1983"),
            references=[
                Reference(Output(text="correct_citation"), tags=[CORRECT_TAG])
            ],
            split=TEST_SPLIT,
            id="clerc_001"
        )
        instances.append(instance)

        return instances
```

**Extension Points:**
- Subclass `Scenario`
- Implement `get_instances(output_path)` method
- Set `name`, `description`, `tags` class variables
- Optional: Override `get_metadata()` for custom display

### B. Adding Custom Run Specs

**Pattern:** Create function decorated with `@run_spec_function(name)`

**Location:** Create new file `/src/helm/benchmark/run_specs/legal_10_run_specs.py`

**Template:**

```python
from helm.benchmark.run_spec import RunSpec, run_spec_function
from helm.benchmark.scenarios.scenario import ScenarioSpec
from helm.benchmark.adaptation.adapter_spec import ADAPT_GENERATION
from helm.benchmark.adaptation.common_adapter_specs import (
    get_generation_adapter_spec,
    get_multiple_choice_adapter_spec
)
from helm.benchmark.metrics.common_metric_specs import (
    get_exact_match_metric_specs,
    get_f1_metric_specs
)

@run_spec_function("legal_10_clerc")
def get_legal_10_clerc_spec() -> RunSpec:
    """S3: Known Authority - CLERC dataset"""
    scenario_spec = ScenarioSpec(
        class_name="helm.benchmark.scenarios.legal_10_clerc_scenario.Legal10CLERCScenario",
        args={}
    )

    adapter_spec = get_generation_adapter_spec(
        instructions="Retrieve the legal authority for the given citation.",
        input_noun="Citation",
        output_noun="Authority",
        max_tokens=512
    )

    metric_specs = get_exact_match_metric_specs()

    return RunSpec(
        name="legal_10_clerc",
        scenario_spec=scenario_spec,
        adapter_spec=adapter_spec,
        metric_specs=metric_specs,
        groups=["legal_10", "legal_10_rag"]
    )

@run_spec_function("legal_10_casehold")
def get_legal_10_casehold_spec() -> RunSpec:
    """S7: Distinguish Cases - CaseHOLD dataset"""
    scenario_spec = ScenarioSpec(
        class_name="helm.benchmark.scenarios.casehold_scenario.CaseHOLDScenario",
        args={}
    )

    adapter_spec = get_multiple_choice_adapter_spec(
        method="ADAPT_MULTIPLE_CHOICE_JOINT",
        instructions="Which holding is most relevant?",
        input_noun="Passage",
        output_noun="Answer",
        max_train_instances=2
    )

    metric_specs = get_exact_match_metric_specs()

    return RunSpec(
        name="legal_10_casehold",
        scenario_spec=scenario_spec,
        adapter_spec=adapter_spec,
        metric_specs=metric_specs,
        groups=["legal_10", "legal_10_closed_book"]
    )
```

**Discovery Mechanism:**
- Decorator `@run_spec_function("name")` automatically registers the function
- Dynamic discovery via `discover_run_spec_functions()` scans all `run_specs/` modules
- Invoked via CLI: `helm-run --run-specs legal_10_clerc`

### C. Adding Custom Metrics (Optional)

**Pattern:** Subclass `Metric` and implement `evaluate()` method

**Location:** Create `/src/helm/benchmark/metrics/legal_10_metrics.py`

**Template:**

```python
from typing import List
from helm.benchmark.metrics.metric import (
    Metric, MetricSpec, MetricResult, PerInstanceStats, Stat
)
from helm.benchmark.metrics.metric_name import MetricName
from helm.benchmark.adaptation.scenario_state import ScenarioState
from helm.benchmark.metrics.metric_service import MetricService

class CitationValidationMetric(Metric):
    """Custom metric for citation integrity (S9)"""

    def evaluate(
        self,
        scenario_state: ScenarioState,
        metric_service: MetricService,
        eval_cache_path: str,
        parallelism: int
    ) -> MetricResult:
        # Custom evaluation logic
        stats = []
        per_instance_stats = []

        for request_state in scenario_state.request_states:
            # Validate citation exists and is correctly formatted
            citation_valid = self._validate_citation(request_state.result.completions[0].text)

            per_instance_stats.append(PerInstanceStats(
                instance_id=request_state.instance.id,
                trial_index=0,
                stats=[Stat(MetricName("citation_valid")).add(1 if citation_valid else 0)]
            ))

        # Aggregate stats
        total_valid = sum(1 for ps in per_instance_stats if ps.stats[0].sum == 1)
        stats.append(Stat(MetricName("citation_accuracy")).add(total_valid / len(per_instance_stats)))

        return MetricResult(aggregated_stats=stats, per_instance_stats=per_instance_stats)

    def _validate_citation(self, text: str) -> bool:
        # Implementation for citation validation
        return True  # Placeholder

# Helper function for run specs
def get_legal_10_citation_metric_specs() -> List[MetricSpec]:
    return [
        MetricSpec(
            class_name="helm.benchmark.metrics.legal_10_metrics.CitationValidationMetric",
            args={}
        )
    ]
```

**When to Create Custom Metrics:**
- Only if existing metrics (exact_match, F1, ROUGE, etc.) don't suffice
- For Legal-10: May need custom metrics for S9 (Citation Integrity) and S1/S2 (Research Planning/Stopping)

### D. Adapters (Usually NOT Needed)

**Note:** HELM provides standard adapters that handle most use cases:
- `GenerationAdapter` - For open-ended generation (most Legal-10 tasks)
- `MultipleChoiceJointAdapter` - For multiple choice (S7: CaseHOLD)
- `ChatAdapter` - For chat-based models

Only create custom adapters if you need specialized prompt formatting beyond what `adapter_spec.instructions` provides.

---

## 3. Data Flow Architecture

Complete flow from input to output (unchanged by Legal-10):

```
┌─────────────────────────────────────────────────────────────┐
│ Input: RunSpec (name, scenario, adapter, metrics)           │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│ SCENARIO CREATION                                            │
│ Runner.run_one() → create_scenario(scenario_spec)           │
│ → Calls Scenario.get_instances(output_path)                │
│ Returns: List[Instance]                                     │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│ DATA PREPROCESSING (Optional)                               │
│ DataPreprocessor.preprocess(instances, parallelism)        │
│ Applies data augmentations if specified                     │
│ Returns: List[Instance]                                    │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│ ADAPTATION: Instance → Request                              │
│ AdapterFactory.get_adapter(adapter_spec)                    │
│ → Adapter.adapt(instances, parallelism)                     │
│ Converts each Instance to RequestState(s)                   │
│ Returns: ScenarioState (list of RequestState)               │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│ EXECUTION: Request → Result                                 │
│ Executor.execute(scenario_state)                            │
│ - Parallel execution (parallelism parameter)                │
│ - Makes API/local calls via client                          │
│ - Populates RequestState.result                             │
│ Returns: ScenarioState (with results filled in)             │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│ METRICS EVALUATION                                          │
│ For each MetricSpec:                                        │
│   metric = create_metric(metric_spec)                       │
│   metric.evaluate(scenario_state, metric_service, ...)     │
│ Returns: MetricResult                                       │
│ - aggregated_stats: List[Stat] (global metrics)             │
│ - per_instance_stats: List[PerInstanceStats] (per item)    │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│ Output Files (written to benchmark_output/runs/):           │
│ - run_spec.json (RunSpec as JSON)                           │
│ - scenario.json (Scenario instances)                        │
│ - scenario_state.json (RequestState list)                   │
│ - stats.json (aggregated metrics)                           │
│ - per_instance_stats.json (per-item metrics)                │
└─────────────────────────────────────────────────────────────┘
```

**Key Data Structures:**
1. **Instance:** Single input data point with references
2. **RequestState:** Instance + Request + Result + metadata
3. **ScenarioState:** Collection of RequestStates (immutable)
4. **Stat:** Single metric value with MetricName context
5. **PerInstanceStats:** Stats grouped by (instance_id, trial_index)

---

## 4. Configuration & Discovery Systems

### A. Run Spec Discovery

- **Location:** All files in `/src/helm/benchmark/run_specs/`
- **Mechanism:** Decorator `@run_spec_function("name")` registers automatically
- **Discovery:** `discover_run_spec_functions()` uses `pkgutil.iter_modules()` to scan package
- **Registry:** `_REGISTERED_RUN_SPEC_FUNCTIONS` dict
- **Access:** `get_run_spec_function(name)` looks up by name

**For Legal-10:** Create `legal_10_run_specs.py` in `run_specs/` directory

### B. Model Metadata Discovery

- **Built-in:** `/src/helm/config/model_metadata.yaml`
- **Local Override:** `$LOCAL_PATH/model_metadata.yaml`
- **Format:** YAML with `models` list of `ModelMetadata` objects
- **Registration:** `register_model_metadata(metadata)` adds to `ALL_MODELS_METADATA`
- **Access:** `get_model_metadata(name)` retrieves by name

### C. Model Deployment Discovery

- **Built-in:** `/src/helm/config/model_deployments.yaml`
- **Local Override:** `$LOCAL_PATH/model_deployments.yaml`
- **Format:** YAML with `model_deployments` list + client_spec class_name
- **Registration:** `register_model_deployment(deployment)` adds to registry
- **Access:** `get_model_deployment(name)` retrieves by name

### D. Scenario Discovery

- **NO central registry** - scenarios found via `ScenarioSpec.class_name`
- **Pattern:** `class_name: "helm.benchmark.scenarios.module.ClassName"`
- **Loading:** `create_scenario(scenario_spec)` → `create_object()` → import + instantiate

---

## 5. Legal-10 Specific Implementation Roadmap

### Legal-10 Skills Mapping

| Skill | Name | Dataset | Modality | Status |
|-------|------|---------|----------|--------|
| **S1** | Research Planning | LegalAgentBench | AG | To Implement |
| **S2** | Strategic Stopping | LegalAgentBench | AG | To Implement |
| **S3** | Known Authority | CLERC | RAG | To Implement |
| **S4** | Unknown Authority | CLERC (semantic) | RAG | To Implement |
| **S5** | Validate Authority | KeyCite-CLERC | RAG | To Implement |
| **S6** | Fact Extraction | CUAD | RAG | To Implement |
| **S7** | Distinguish Cases | CaseHOLD | CB | ✅ Exists |
| **S8** | Synthesize Results | LEXam | CB | To Implement |
| **S9** | Citation Integrity | Dahl 10-types | CB | To Implement |
| **S10** | Copyright Compliance | SHIELD | CB | To Implement |

### Implementation Checklist

**Phase 1: Setup**
- [ ] Create `/src/helm/benchmark/scenarios/legal_10/` subdirectory (optional)
- [ ] Create `/src/helm/benchmark/run_specs/legal_10_run_specs.py`
- [ ] Create `/docs/legal_10_overview.md`
- [ ] Update `/mkdocs.yml` navigation

**Phase 2: Closed-Book Scenarios (S7-S10)**
- [ ] S7: Verify existing `casehold_scenario.py` works
- [ ] S8: Create `legal_10_lexam_scenario.py`
- [ ] S9: Create `legal_10_citation_scenario.py`
- [ ] S10: Create `legal_10_shield_scenario.py`

**Phase 3: RAG Scenarios (S3-S6)**
- [ ] S3, S4: Create `legal_10_clerc_scenario.py`
- [ ] S5: Create `legal_10_keycite_scenario.py`
- [ ] S6: Create `legal_10_cuad_scenario.py`

**Phase 4: Agentic Scenarios (S1-S2)**
- [ ] S1, S2: Create `legal_10_agentbench_scenario.py`
- [ ] Consider custom metrics for research planning quality

**Phase 5: Integration**
- [ ] Create all `@run_spec_function` entries in `legal_10_run_specs.py`
- [ ] Add Legal-10 group to `/src/helm/benchmark/static/schema_legal.yaml`
- [ ] Create run entries in `/src/helm/benchmark/presentation/run_entries_legal.conf`
- [ ] Write unit tests for each scenario

**Phase 6: Documentation**
- [ ] Complete `/docs/legal_10_setup.md`
- [ ] Complete `/docs/legal_10_scenarios.md`
- [ ] Update skill pages with implementation details

---

## 6. DO NOT TOUCH vs SAFE TO EXTEND Summary

### ❌ ABSOLUTELY DO NOT MODIFY

1. **Frozen dataclasses:** `RunSpec`, `AdapterSpec`, `Instance`, `Reference`, `Output`, `Input`, `RequestState`, `ScenarioState`
2. **Abstract base classes:** `Scenario.get_instances()`, `Adapter.adapt()`, `Metric.evaluate()`
3. **Core execution:** `Runner.run_one()`, `Executor.execute()`
4. **Registries and discovery:** `@run_spec_function` decorator, `discover_run_spec_functions()`, config registry
5. **Factory patterns:** `AdapterFactory.get_adapter()`, `create_object()`, `create_scenario()`
6. **Core routing:** Model metadata/deployment registries

### ✅ SAFE TO EXTEND (Create new, don't modify existing)

1. **Scenarios:** Create new `*_scenario.py` files, implement `Scenario` subclass
2. **Run Specs:** Create new `*_run_specs.py` files, use `@run_spec_function()` decorator
3. **Metrics:** Create new `*_metrics.py` files, subclass `Metric`, provide `MetricSpec` helper
4. **Configuration:** Add local YAML files, don't modify built-in config YAML
5. **Documentation:** Add new `.md` files, update navigation in `mkdocs.yml`

---

## 7. Testing Your Integration

### Verify Run Spec Discovery

```bash
# List all available run specs (should include legal_10_*)
helm-run --help

# Describe a specific Legal-10 run spec
helm-run --run-specs legal_10_clerc --describe
```

### Run a Single Scenario

```bash
# Run Legal-10 CaseHOLD scenario
helm-run \
  --run-specs legal_10_casehold \
  --suite legal_10 \
  --max-eval-instances 10 \
  --output-path benchmark_output/test_run
```

### Run All Legal-10 Scenarios

```bash
# Run complete Legal-10 suite
helm-run \
  --run-specs legal_10_clerc,legal_10_casehold,legal_10_lexam \
  --suite legal_10 \
  --models-to-run anthropic/claude-3-sonnet-20240229 \
  --output-path benchmark_output/legal_10_full
```

---

## 8. Integration Best Practices

### Naming Conventions

| Component | Convention | Example |
|-----------|-----------|---------|
| Scenario file | `legal_10_<dataset>_scenario.py` | `legal_10_clerc_scenario.py` |
| Scenario class | `Legal10<Name>Scenario` | `Legal10CLERCScenario` |
| Run spec function | `get_legal_10_<name>_spec()` | `get_legal_10_clerc_spec()` |
| Run spec name | `legal_10_<name>` | `"legal_10_clerc"` |
| Groups | `["legal_10", "legal_10_<modality>"]` | `["legal_10", "legal_10_rag"]` |
| Tags | Lowercase, hyphenated | `["legal", "retrieval"]` |

### Import Patterns

**Standard imports for scenarios:**
```python
from typing import List
from helm.benchmark.scenarios.scenario import (
    Scenario, Instance, Input, Output, Reference,
    TRAIN_SPLIT, TEST_SPLIT, VALID_SPLIT, CORRECT_TAG
)
from helm.common.general import ensure_directory_exists
```

**Standard imports for run specs:**
```python
from helm.benchmark.run_spec import RunSpec, run_spec_function
from helm.benchmark.scenarios.scenario import ScenarioSpec
from helm.benchmark.adaptation.common_adapter_specs import (
    get_generation_adapter_spec,
    get_multiple_choice_adapter_spec
)
from helm.benchmark.metrics.common_metric_specs import (
    get_exact_match_metric_specs,
    get_f1_metric_specs
)
```

### Error Handling

- Scenarios should raise `ValueError` for invalid configuration
- Use `hlog()` from `helm.common.hierarchical_logger` for logging
- Let exceptions propagate - HELM's runner handles them gracefully

### Performance

- Use `ensure_directory_exists()` before file operations
- Cache downloaded datasets in `output_path`
- Use `parallelism` parameter for concurrent processing
- Avoid loading entire datasets into memory if possible

---

## Conclusion

HELM's architecture provides clear separation between:
- **Framework code** (do not touch)
- **Extension points** (safe to add Legal-10 content)

By following these guidelines, Legal-10 integrates seamlessly without modifying any core HELM functionality. All Legal-10 components are:

- **Modular:** Each skill is an independent scenario
- **Discoverable:** Run specs auto-register via decorator
- **Maintainable:** Clear separation from core framework
- **Upgradeable:** HELM updates won't break Legal-10 code

The key is to **extend, never modify** - inherit from base classes, use decorators, and create new files rather than changing existing ones.
