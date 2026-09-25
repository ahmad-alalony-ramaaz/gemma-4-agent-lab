# Baseline Architecture: Analyzer + Coder

This document describes the baseline agent architecture implemented in Experiment 001 for the **Google - The Gemma 4 Developer Agent Competition**.

All behaviors and mechanisms documented here are verified directly from the agent specification files (`agent.yaml`, `code_analyzer.yaml`, `prompts/system.md`, `prompts/analyzer.md`, and `configs/sampling.yaml`).

---

## Architectural Overview

The baseline system employs a decoupled two-agent architecture designed to solve real-world software engineering issues in sandboxed Git repositories:

```text
Issue Description
       │
       ▼
 ┌───────────┐
 │ swe_coder │ ◄── [Primary Coordinator & Solver]
 └─────┬─────┘
       │ delegates initial localization
       ▼
 ┌─────────────────┐
 │  code_analyzer  │ ◄── [Read-Only Navigation Specialist in Isolated Context]
 └─────┬───────────┘
       │
       │ searches codebase via:
       │ • run_command (git grep, grep, sed, ls)
       │ • search_similar_code (symbol matching)
       │ • get_code_neighbors & get_code_subgraph (AST call-graph traversal)
       │ • read_file (tight line ranges)
       │
       ▼
 Concise Report (< 250 words):
 LOCATION / ROOT CAUSE / FIX PLAN / RELATED / TESTS / CONFIDENCE
       │
       ▼
 ┌───────────┐
 │ swe_coder │
 └─────┬─────┘
       │
       ├── 1. Verify Claim (read_file on exact lines)
       ├── 2. Reproduce (minimal /tmp/repro.py via run_command)
       ├── 3. Edit (edit_file / write_file inside /workspace)
       ├── 4. Focused Verification (py_compile, rerun repro, pytest -x -q)
       ├── 5. Budget Check (get_status every ~8 calls)
       └── 6. Finalization (git status/diff check, clean workspace)
       │
       ▼
  submit_patch
```

---

## The Role of the Analyzer and Context Isolation

In software-engineering benchmarks with large codebases, repository exploration (grepping, walking directory trees, querying symbol indices, reading multiple files) generates substantial verbose output. 

If this exploration occurred directly inside the primary coder's context:
1. The **32,768-token context window** would rapidly become consumed by intermediate grep traces and irrelevant code blocks.
2. The model's attention would degrade as the conversation history lengthened, increasing the probability of hallucinations, syntax mistakes, or losing track of the issue specification.

### Context Isolation Mechanism
To solve this, `swe_coder` invokes `code_analyzer` as an `agent_tool` (`skip_summarization: true`):
- `code_analyzer` executes in its own independent context window.
- It performs iterative discovery, searches symbols, inspects call graphs, and reads candidate lines.
- It compresses its findings into a strict, structured summary of **at most 250 words**:
  - `LOCATION`: Exact file path and line numbers with function/class name.
  - `ROOT CAUSE`: One or two sentences explaining why behavior diverges.
  - `FIX PLAN`: Concrete proposed changes.
  - `RELATED`: Other call sites or files needing corresponding adjustments.
  - `TESTS`: Existing test files exercising the target logic.
  - `CONFIDENCE`: Assessment level (`high`, `medium`, or `low`).
- Only this structured 250-word synthesis enters `swe_coder`'s context, leaving the remaining context budget fully available for reproduction, implementation, and verification.

---

## Agent Roles & Verified Execution Protocols

### 1. `code_analyzer` (Read-Only Sub-Agent)
Declared in `sub_agents/code_analyzer.yaml` and instructed via `prompts/analyzer.md`:
- **Read-Only Invariant**: Never modifies files.
- **Available Tools**:
  - `run_command`: Restricted strictly to read-only shell commands (`git grep -n`, `grep -rn`, `ls`, `sed -n`). Note: `rg` is not installed; git history is truncated at the current commit.
  - `search_similar_code`: Symbol-based lookup (e.g., `APIKeyHeader`), verified by reading actual code.
  - `get_code_neighbors`: Traverses synchronous caller/callee edges.
  - `get_code_subgraph`: Connects candidate symbols to understand structural relationships.
  - `read_file`: Reads specific line ranges to confirm hypotheses.
- **Fallback Behavior**: If a code graph tool encounters an error, the agent stops using graph tools and falls back directly to text searches (`git grep`, `grep`).
- **Methodology**: Extracts symbols and error messages from the issue, searches identifiers without repeating failed terms, traces call chains, and validates exact line numbers.

---

### 2. `swe_coder` (Primary Autonomous Engineer)
Declared in `agent.yaml` and instructed via `prompts/system.md`:
- **Session Rules**:
  - Every reply must contain a tool call until `submit_patch` is invoked (3 consecutive replies without tool calls terminate the session with the current tree graded as-is).
  - No human answers questions; the agent acts autonomously.
  - Patch is frozen once `submit_patch` is called.
- **Hard Constraints**:
  - Never modify, add, or delete test suites, `conftest.py`, `pytest.ini`, CI, or packaging configurations. Hidden validation tests are executed post-submission.
  - Maintain backward compatibility for public APIs unless explicitly directed otherwise.
  - Scratch files must reside exclusively in `/tmp` via `run_command` (`read_file`, `write_file`, and `edit_file` only accept paths within `/workspace`).
  - Pre-built offline environment: no package installations.
- **Execution Workflow**:
  1. **Understand**: State expected vs. actual behavior internally, ignoring boilerplate PR template text.
  2. **Localize**: Call `code_analyzer` with the full issue text, then verify returned locations by reading the exact lines. Search additional identifiers if necessary.
  3. **Reproduce**: Create a minimal standalone script in `/tmp/repro.py` via `run_command` and run it against the active environment, confirming package import paths point inside `/workspace`.
  4. **Fix**: Apply targeted changes with `edit_file` using unique, verbatim code chunks with leading whitespace preserved. When new modules or documentation examples are required, create them at their real repository paths using `write_file`.
  5. **Verify**: Check syntax using `python -m py_compile <file>`, re-run `/tmp/repro.py`, and run the closest existing test files (`python -m pytest <tests/path> -x -q -p no:anyio`, narrowing with `-k`).
  6. **Submit**: Run `git status --short` and `git diff` to ensure no accidental scratch files remain in `/workspace`, then invoke `submit_patch`.
- **Budget Discipline**:
  - Query remaining budget via `get_status` every ~8 tool calls.
  - If fewer than 25% of turns or time remain, abort further exploration and proceed immediately to Fix → Verify → Submit.
  - Keep command outputs compact (`head`, `grep -n`, `pytest -q`).
  - If an edit fails twice, print the exact lines with `sed` and rewrite with a smaller snippet or fallback script.

---

## Model & Inference Parameters

- **Base Model**: `gemma-4-31b-it-qat-w4a16-ct`
- **Context Window**: 32,768 tokens
- **Sampling Parameters** (from `configs/sampling.yaml`):
  - `temperature`: `0.2`
  - `top_p`: `0.95`
  - `top_k`: `40`
  - `max_output_tokens`: `8192`
  - `thinking_budget`: `4096` (`include_thoughts: false`)
