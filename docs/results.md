# Benchmark Results & Experiment Log

This document tracks public benchmark evaluations for all agent configurations submitted to the **Google - The Gemma 4 Developer Agent Competition**.

> [!IMPORTANT]
> Official Kaggle public leaderboard scores are recorded only after evaluation runs conclude. No benchmark values are estimated or invented.

---

## Leaderboard & Experiment Summary Table

| Exp ID | Experiment Name | Primary Mutation / Architecture | Submitted (UTC) | Public Score | Delta | Status |
|:---:|:---|:---|:---:|:---:|:---:|:---:|
| **001** | [Baseline (Analyzer + Coder)](../experiments/001-baseline/) | Analyzer + Coder baseline | 2026-09-25 20:14 | **0.06** | Baseline | Completed |
| **002** | [Evidence-Gated Verification](../experiments/002-evidence-gated-verification/) | Verification becomes evidence-gated | Not recorded | **0.08** | +0.02 | Completed |
| *003* | *Adaptive Analyzer* | *Dynamic analyzer gating based on issue specificity* | — | — | — | Planned |
| *004* | *Progressive Localization* | *Hierarchical multi-stage localization pipeline* | — | — | — | Planned |
| *005* | *Reviewer Agent* | *Independent post-edit validation and review agent* | — | — | — | Planned |

> [!NOTE]
> Experiment 001 defines the initial baseline. Score deltas for subsequent experiments will be measured relative to 0.06 and/or the immediately preceding experiment, depending on the experiment design.

> [!NOTE]
> **Roadmap note (2026-09-27):** The preliminary roadmap listed Adaptive Analyzer, Progressive Localization and Reviewer Agent as Experiments 002, 003 and 004. After the Experiment 001 diagnostic audit identified a stronger evidence-backed verification gap, Evidence-Gated Verification was scheduled as Experiment 002, and the three planned experiments moved to 003, 004 and 005. The earlier roadmap was a preliminary plan that was refined once evidence became available.

---

## Detailed Experiment Logs

### Experiment 001: Baseline (Analyzer + Coder)
- **Model**: `gemma-4-31b-it-qat-w4a16-ct`
- **Architecture**: Analyzer + Coder baseline
- **Submission Description**: Baseline v1 - analyzer + coder starter
- **Submission Bundle SHA256**: `569bddd8afe75ec604e69ee01cda3fddee377ce97368fda72b75ba14f218bd5e`
- **Submission Date**: 2026-09-25T20:14:00+00:00
- **Kaggle Submission Status**: Succeeded
- **Public Score**: 0.06
- **Delta**: Baseline
- **Status**: Completed
- **Notes**: Established the reference benchmark. The Kaggle evaluation completed successfully with a Public Score of 0.06.

### Experiment 002: Evidence-Gated Verification
- **Model**: `gemma-4-31b-it-qat-w4a16-ct`
- **Architecture**: Analyzer + Coder (unchanged from Experiment 001)
- **Single Primary Change**: Verification becomes evidence-gated (three lines of `agent/prompts/system.md`)
- **Prompt SHA-256 Prefix**: `614e4ead2122` (Experiment 001: `d51393ea9c3f`)
- **Hypothesis**: Evidence-gated verification may reduce submission of edits that the agent has observed still failing.
- **Submission Description**: Experiment 002 - Evidence-Gated Verification
- **Submission Bundle SHA256**: Not recorded. The hash of the bundle accepted by Kaggle is unknown and may differ from the local candidate bundle recorded in `experiment_card.json` under `candidate_bundle`.
- **Submission Date**: Not recorded
- **Kaggle Submission Status**: Succeeded
- **Result (Public Score)**: 0.08
- **Baseline**: 0.06 (Experiment 001)
- **Observed Delta**: +0.02
- **Status**: Completed
- **Interpretation**: Positive observed signal, consistent with the hypothesis, but not sufficient to establish causality because run-to-run variance is unknown.
- **Decision**: Retain Experiment 002 as the highest observed configuration so far and close the experiment. Do not start the next experiment until its hypothesis is selected separately.
- **Notes**: Prepared from the frozen Experiment 001 agent artifacts. `agent.yaml`, `prompts/analyzer.md`, `sub_agents/code_analyzer.yaml` and `configs/sampling.yaml` are byte-for-byte identical to Experiment 001. Only one scored run exists for each experiment. No per-task conclusions are drawn from the hidden evaluation.

---

*This table will be updated sequentially as official evaluation results become available.*
