# Benchmark Results & Experiment Log

This document tracks public benchmark evaluations for all agent configurations submitted to the **Google - The Gemma 4 Developer Agent Competition**.

> [!IMPORTANT]
> Official Kaggle public leaderboard scores are recorded only after evaluation runs conclude. No benchmark values are estimated or invented.

---

## Leaderboard & Experiment Summary Table

| Exp ID | Experiment Name | Primary Mutation / Architecture | Submitted (UTC) | Public Score | Delta | Status |
|:---:|:---|:---|:---:|:---:|:---:|:---:|
| **001** | [Baseline (Analyzer + Coder)](../experiments/001-baseline/) | Analyzer + Coder baseline | 2026-09-25 20:14 | **0.06** | Baseline | Completed |
| **002** | [Evidence-Gated Verification](../experiments/002-evidence-gated-verification/) | Verification becomes evidence-gated | — | Pending | — | Prepared / Not Submitted |
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
- **Submission Description**: Pending
- **Submission Bundle SHA256**: Pending (local candidate built; not yet validated or submitted)
- **Submission Date**: Pending
- **Kaggle Submission Status**: Not submitted
- **Public Score**: Pending
- **Delta**: Pending
- **Status**: Prepared / Not Submitted
- **Notes**: Prepared from the frozen Experiment 001 agent artifacts. `agent.yaml`, `prompts/analyzer.md`, `sub_agents/code_analyzer.yaml` and `configs/sampling.yaml` are byte-for-byte identical to Experiment 001. No score has been recorded.

---

*This table will be updated sequentially as official evaluation results become available.*
