# Experiment 001 — Baseline

## Status
Completed

- **Kaggle submission status**: Succeeded
- **Public Score**: 0.06

## Purpose
Establish a reproducible baseline before making any optimization.

## Architecture

```text
Issue
  ↓
SWE Coder
  ↓
Code Analyzer
  ↓
Search / Graph / Read
  ↓
SWE Coder
  ↓
Reproduce / Edit / Test
  ↓
submit_patch
```

### Agent Responsibilities

- **`swe_coder` (Primary Solver & Coordinator)**:
  - Operates as an autonomous software engineer inside the sandboxed `/workspace`.
  - Receives the raw issue prompt and invokes `code_analyzer` as its initial step to localize the problem.
  - Confirms candidate locations by reading exact file lines before applying any modifications.
  - Generates minimal reproduction scripts in `/tmp/repro.py` via `run_command` and executes them against the environment.
  - Applies surgical code edits with `edit_file` while strictly maintaining backward compatibility and avoiding modifications to test files, CI, or packaging configurations.
  - Validates syntax using `python -m py_compile`, reruns the reproduction script, and runs the closest existing `pytest` test suites.
  - Monitors remaining turns and time by calling `get_status` approximately every 8 tool invocations.
  - Ensures `/workspace` remains clean of temporary files before invoking `submit_patch` to freeze and score the patch.

- **`code_analyzer` (Read-Only Sub-Agent Specialist)**:
  - Serves as a read-only code navigation specialist invoked by `swe_coder` via `agent_tool` (`skip_summarization: true`).
  - Executes repo-level searches using read-only shell commands (`git grep -n`, `grep -rn`, `sed -n`), symbol queries (`search_similar_code`), and AST call-graph inspections (`get_code_neighbors`, `get_code_subgraph`).
  - Operates within its own isolated context window, preventing broad exploration traces and multi-file grep outputs from exhausting `swe_coder`'s context budget.
  - Returns a strictly constrained response (maximum 250 words) structured into:
    - `LOCATION`: `<path>:<start>-<end> (<function or class>)`
    - `ROOT CAUSE`: Summary of the diverging behavior
    - `FIX PLAN`: Concrete proposed changes
    - `RELATED`: Additional call sites or files requiring updates
    - `TESTS`: Existing test files exercising the target code
    - `CONFIDENCE`: `high` | `medium` | `low`

---

## Configuration

The configuration parameters recorded below are taken directly from `experiment_card.json` and the corresponding agent definition files:

- **Model Alias**: `gemma-4-31b-it-qat-w4a16-ct`
- **Architecture**: `analyzer+coder`
- **Context Limit**: `32768` tokens
- **Sampling Configuration**:
  - `temperature`: `0.2`
  - `top_p`: `0.95`
  - `top_k`: `40`
  - `max_output_tokens`: `8192`
  - `thinking_budget`: `4096` tokens (`include_thoughts: false`)
- **Available Tools**:
  - Overall bundle tools: `run_command`, `submit_patch`, `get_status`, `read_file`, `edit_file`, `write_file`, `search_similar_code`, `get_code_neighbors`, `get_code_subgraph`
  - Invocable by `swe_coder`: `run_command`, `read_file`, `edit_file`, `write_file`, `get_status`, `submit_patch`, and `code_analyzer` (as an `agent_tool`)
  - Invocable by `code_analyzer`: `run_command` (read-only usage), `read_file`, `search_similar_code`, `get_code_neighbors`, `get_code_subgraph`
- **Validation Status**: `validation_passed: true`
- **Prompt Hash (SHA prefix)**: `d51393ea9c3f`
- **Submission Bundle SHA256**: `569bddd8afe75ec604e69ee01cda3fddee377ce97368fda72b75ba14f218bd5e`
- **Creation Timestamp**: `2026-09-25T20:14:00+00:00`
- **Bundle Base**: `starter`

---

## Result

- The Kaggle evaluation completed successfully.
- **Kaggle submission status**: Succeeded
- **Public Score**: 0.06
- This score establishes the baseline for subsequent experiments.

---

## Interpretation

- The purpose of Experiment 001 was to establish a reproducible measurement before optimization.
- The score of 0.06 is now the reference comparison point for future experiments.
- No causal conclusions about the score are being made yet.
- Future experiments will change one primary variable at a time and compare their score against 0.06.

---

## Next Experiments

The following are planned iterative experiments to be tested against this baseline:

- **002 — Adaptive Analyzer**: *(Planned)* Dynamically gate analyzer invocation based on issue specificity and token estimates to conserve budget on self-contained issues.
- **003 — Progressive Localization**: *(Planned)* Implement hierarchical multi-stage localization (file level → class/function level → statement level) before handing off to the coder.
- **004 — Reviewer Agent**: *(Planned)* Introduce an independent code review and regression checking sub-agent before calling `submit_patch`.
