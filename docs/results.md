# Benchmark Results & Experiment Log

This document tracks public benchmark evaluations for all agent configurations submitted to the **Google - The Gemma 4 Developer Agent Competition**.

> [!IMPORTANT]
> Official Kaggle public leaderboard scores are recorded only after evaluation runs conclude. No benchmark values are estimated or invented.

---

## Leaderboard & Experiment Summary Table

| Exp ID | Experiment Name | Primary Mutation / Architecture | Submitted (UTC) | Public Score | Delta | Status |
|:---:|:---|:---|:---:|:---:|:---:|:---:|
| **001** | [Baseline (Analyzer + Coder)](../experiments/001-baseline/) | Analyzer + Coder baseline | 2026-09-25 20:14 | **0.06** | Baseline | Completed |
| *002* | *Adaptive Analyzer* | *Dynamic analyzer gating based on issue specificity* | — | — | — | Planned |
| *003* | *Progressive Localization* | *Hierarchical multi-stage localization pipeline* | — | — | — | Planned |
| *004* | *Reviewer Agent* | *Independent post-edit validation and review agent* | — | — | — | Planned |

> [!NOTE]
> Experiment 001 defines the initial baseline. Score deltas for subsequent experiments will be measured relative to 0.06 and/or the immediately preceding experiment, depending on the experiment design.

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

---

*This table will be updated sequentially as official evaluation results become available.*
