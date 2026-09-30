# gemma-4-agent-lab

An open engineering experiment log documenting the systematic evolution and benchmarking of autonomous software-engineering agents for the **Google - The Gemma 4 Developer Agent Competition**.

---

## Overview

`gemma-4-agent-lab` documents controlled, empirical experiments designed to evaluate and iteratively improve an autonomous developer agent powered by Google's Gemma 4 model family.

In software-engineering agent benchmarks, compounding multiple modifications simultaneously obscures what actually drove performance gains or regressions. This repository serves as a transparent lab notebook where each experiment isolates a specific hypothesis, configuration, or architectural modification.

### Key Goals
- **Systematic Improvement**: Progressively optimize the agent's issue localization, patching accuracy, test reproduction, and token budget efficiency.
- **Controlled Comparison**: Measure the empirical impact of each isolated change against a reproducible baseline.
- **Reproducibility**: Maintain complete prompt definitions, tool signatures, agent configurations, and experiment metadata cards for every submission.

---

## Experimental Methodology

Our experimental protocol follows a disciplined scientific cycle:
1. **Single-Variable Mutation**: Change exactly one meaningful variable at a time (e.g., prompting strategy, localization mechanism, sub-agent decomposition, tool budget).
2. **Pre-Submission Validation**: Validate agent configuration, prompt token budgets, and tool schemas against harness checks.
3. **Kaggle Submission & Logging**: Submit the bundled agent to the Kaggle evaluation pipeline and archive the exact prompt SHA and configuration card.
4. **Result Recording & Analysis**: Once official evaluation completes, record the public score, calculate deltas, and analyze failure modes before planning the next iteration.

---

## Baseline Architecture & Model

- **Model**: `gemma-4-31b-it-qat-w4a16-ct` (quantized 4-bit weights / 16-bit activations Gemma 4 31B instruction-tuned model running inside the competition inference container).
- **Architecture**: **Analyzer + Coder** two-agent pipeline.
  - **`code_analyzer`**: A read-only sub-agent tasked with searching the repository, tracing call graphs, and returning a concise localization and fix plan (< 250 words) within a separate context.
  - **`swe_coder`**: The primary coordinator agent that receives the issue description, delegates initial exploration to `code_analyzer`, writes a reproduction script in `/tmp`, applies code edits, executes targeted pytest tests, and submits the final unified patch.

### Original Baseline
- **Experiment**: 001
- **Architecture**: Analyzer + Coder
- **Kaggle submission status**: Succeeded
- **Public Score**: 0.06

The score of 0.06 remains the original reference measurement against which controlled experiments are compared.

### Current Highest Observed Result
- **Experiment**: 002 — Evidence-Gated Verification
- **Architecture**: Analyzer + Coder (unchanged)
- **Kaggle submission status**: Succeeded
- **Public Score**: 0.08

```text
Experiment 001   Analyzer + Coder   0.06
        ↓
Evidence-Gated Verification
        ↓
Experiment 002                      0.08

Observed delta: +0.02
```

This is a positive observed signal. It is not causal proof, because repeated-run variance is unknown and only one scored run exists for each experiment.

---

## Experiments

| Experiment | Change | Public Score | Status |
|------------|--------|--------------|--------|
| [001](experiments/001-baseline/) | Analyzer + Coder Baseline | 0.06 | Completed |
| [002](experiments/002-evidence-gated-verification/) | Evidence-Gated Verification | 0.08 | Completed |
| [003](experiments/003-reclaim-output-headroom/) | Reclaim Output Headroom | Pending | Prepared / Not Submitted |
| 004 | Adaptive Analyzer | — | Planned |
| 005 | Progressive Localization | — | Planned |
| 006 | Reviewer Agent | — | Planned |

> **Roadmap note (2026-09-27):** The preliminary roadmap listed Adaptive Analyzer, Progressive Localization and Reviewer Agent as Experiments 002, 003 and 004. After the Experiment 001 diagnostic audit identified a stronger evidence-backed verification gap, Evidence-Gated Verification was scheduled as Experiment 002, and the three planned experiments moved to 003, 004 and 005. The earlier roadmap was a preliminary plan that was refined once evidence became available.

> **Roadmap note (2026-09-30):** After Experiment 002, a harness-source investigation indicated that the `max_output_tokens` reserve may reduce available input headroom. Reclaim Output Headroom was scheduled as Experiment 003, and the three planned experiments moved to 004, 005 and 006. They remain preliminary candidates whose order and design have not been selected.

---

## Repository Structure

```text
gemma-4-agent-lab/
│
├── README.md                           # Main laboratory overview & experiment index
├── CHANGELOG.md                        # Version and experiment history
├── LICENSE-NOTICE.md                   # Licensing details and attribution notices
├── .gitignore                          # Exclusions for competition data, caches & secrets
│
├── experiments/
│   ├── 001-baseline/                  # Experiment 001 (Kaggle baseline submission)
│   │   ├── README.md                   # Detailed experiment 001 report & parameters
│   │   ├── experiment_card.json        # Machine-readable metadata & SHA hashes
│   │   │
│   │   └── agent/                      # Frozen agent bundle for Experiment 001
│   │       ├── agent.yaml              # Primary agent declaration (swe_coder)
│   │       ├── configs/
│   │       │   └── sampling.yaml       # Generation & thinking budget parameters
│   │       ├── prompts/
│   │       │   ├── system.md           # swe_coder system prompt & execution protocol
│   │       │   └── analyzer.md         # code_analyzer specialist prompt
│   │       └── sub_agents/
│   │           └── code_analyzer.yaml  # code_analyzer sub-agent declaration
│   │
│   ├── 002-evidence-gated-verification/  # Experiment 002 (completed, Public Score 0.08)
│   │   ├── README.md                   # Experiment 002 hypothesis, change & risks
│   │   ├── experiment_card.json        # Repository-side experiment metadata
│   │   │
│   │   └── agent/                      # Same layout as 001; only prompts/system.md differs
│   │
│   └── 003-reclaim-output-headroom/    # Experiment 003 (prepared, not yet submitted)
│       ├── README.md                   # Experiment 003 hypothesis, change & limitations
│       ├── experiment_card.json        # Repository-side experiment metadata
│       │
│       └── agent/                      # Same layout as 002; only configs/sampling.yaml differs
│
└── docs/
    ├── architecture.md                 # Baseline dual-agent architectural breakdown
    ├── experiments.md                  # Scientific methodology & documentation protocol
    └── results.md                      # Comprehensive benchmark results tracking
```

---

## Reproduction & Competition Data

To reproduce evaluations or inspect agent execution locally:
1. Competition datasets, task instances (`tasks.jsonl`), prebuilt repository checkout snapshots, AST/graph caches, and test wheels are **intentionally not redistributed** in this repository.
2. All competition data and execution environments must be obtained directly from the official **Google - The Gemma 4 Developer Agent Competition** on Kaggle.
3. The competition dataset is licensed under the **Apache License, Version 2.0**.
4. The configurations and prompts in `experiments/` can be packaged directly into a submission bundle for the official competition harness.

---

## Attribution & Acknowledgments

This repository does not claim to have built the initial starter agent from scratch.

- **Experiment 001 (Baseline)** is derived from the community Kaggle starter notebook:
  > **"Gemma 4 Starter | Inside the Harness + EDA"**  
  > By **Aleksei Provorov**  
  > *Kaggle Notebook URL*: `[https://www.kaggle.com/code/leoprovorov/gemma-4-starter-inside-the-harness-eda]`
- We gratefully acknowledge Aleksei Provorov's work in providing a clear, modular Analyzer + Coder starter implementation for the competition harness. This repository adopts that starter as the baseline for all subsequent controlled experiments.
