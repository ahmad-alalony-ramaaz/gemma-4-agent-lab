# Experiment 002 — Evidence-Gated Verification

- **Experiment ID**: 002
- **Date prepared**: 2026-09-27

## Status

Completed

- **Kaggle submission status**: Succeeded
- **Public Score**: 0.08
- **Baseline Public Score**: 0.06
- **Observed Delta**: +0.02

## Baseline

- **Experiment**: [001 — Baseline (Analyzer + Coder)](../001-baseline/)
- **Public Score**: 0.06

## Observed Problem

The Experiment 001 diagnostic audit was a static reading of the baseline prompts and configuration. It identified two gaps in how the baseline coder prompt (`experiments/001-baseline/agent/prompts/system.md`) handles verification:

1. **No required outcome.** The baseline creates and runs a reproduction script before editing (line 23) and reruns it after editing (line 25). Neither instruction states what the run must show. The prompt does not require the reproduction to fail before the fix or to pass after it, and it does not say what to do when verification still fails after an edit.
2. **Repeat-command contradiction.** Line 25 tells the coder to rerun `/tmp/repro.py` after an edit. Line 32 says "Never run the same command twice without changing it." and has no exception. The rerun uses the same command string, so following one instruction literally breaks the other.

These are observations about the prompt text. No run trajectories were available, and no claim is made that these gaps caused the 0.06 score.

## Hypothesis

> If verification becomes evidence-gated while localization, architecture, model, tools, analyzer behavior, and sampling remain unchanged, then the agent may submit a higher proportion of correct patches because edits that are observed still failing will not be treated as verified fixes.

## Independent Variable

Verification becomes evidence-gated.

## What Changed

Only `agent/prompts/system.md` behavior. Three existing lines were modified. No lines, sections, steps or tools were added.

| Line | Step | Change |
|:---:|:---|:---|
| 23 | Reproduce | **Verification evidence before edit.** The reproduction must show the reported wrong behaviour (or the missing feature) before editing. If a minimal script is impractical or cannot be made to show it, the closest existing focused test or a short `python -c "assert ..."` check is used instead. Whichever is used becomes the verification target. The import-path check is preserved. |
| 25 | Verify | **Verification evidence after edit.** The same verification target is rerun and must show the expected behaviour. The closest existing tests then run exactly as in the baseline. If the target still fails, the coder inspects the failure and keeps fixing, and does not submit a change it has watched fail unless the existing low-budget rule applies. |
| 32 | Budget discipline | **Intentional verification reruns allowed.** The repeat-command rule gains the clause "except to rerun verification after an edit". |

| Measure | Experiment 001 | Experiment 002 |
|:---|:---:|:---:|
| Prompt lines | 37 | 37 |
| Prompt words | 712 | 809 |
| Prompt SHA-256 prefix | `d51393ea9c3f` | `614e4ead2122` |

The exact diff can be regenerated from the repository root:

```bash
diff -u experiments/001-baseline/agent/prompts/system.md experiments/002-evidence-gated-verification/agent/prompts/system.md
```

## What Stayed Constant

These four agent files are byte-for-byte identical to Experiment 001:

| File | SHA-256 |
|:---|:---|
| `agent/agent.yaml` | `c8f4305df8091c679985b610d3e5195e12703c5989f61230939a938ef6afb2e4` |
| `agent/prompts/analyzer.md` | `3eb7a6eafba9f6d2d2d74b92822ed0715ebb6188202c41c53765b946392a02d5` |
| `agent/sub_agents/code_analyzer.yaml` | `07530d7f65c9654c3f074924cba10fb3e1fd22115885c3a948decc47fb055fc3` |
| `agent/configs/sampling.yaml` | `ac857a0e8f76e01bfb7de0dcfb1d7d68d256ba1537109050127cf75476ee5012` |

- **Architecture**: Analyzer + Coder, with `code_analyzer` invoked as an `agent_tool` (`skip_summarization: true`).
- **Model**: `gemma-4-31b-it-qat-w4a16-ct` for both agents.
- **Sampling**: `temperature` 0.2, `top_p` 0.95, `top_k` 40, `max_output_tokens` 8192, `thinking_budget` 4096 (`include_thoughts: false`).
- **Context limit**: 32768 tokens.
- **Tools**: the same tool lists for `swe_coder` and `code_analyzer`.
- **Analyzer**: prompt, configuration, invocation rule and output format.
- **Unchanged `system.md` behavior** (the other 34 lines are byte-identical): session rules, hard rules, analyzer invocation, localization, grep strategy, read strategy, editing strategy, `get_status` cadence, the 25% budget threshold, the pytest command, the Submit step, the always-submit rule and the quality bar.

## Expected Signal

A Public Score meaningfully above 0.06 would support further investigation of the hypothesis.

No numeric significance threshold is defined, because run-to-run variance is unknown.

## Negative / Uncertain Signal

- A Public Score at or below 0.06 may weaken the hypothesis.
- A very small change in either direction remains uncertain, because run-to-run variance is unknown.

## Risks

- **Self-authored verification may be weak.** The coder writes its own reproduction or inline check, so a weak assertion can satisfy the gate.
- **Fallback existing tests may already pass before the edit.** Hidden tests are applied after the run, so an existing test gives regression evidence rather than evidence of the requested behaviour.
- **Verification loops may consume budget.** Revising a reproduction or repeating fix attempts costs tool calls. The existing low-budget rule is the only bound.
- **Fewer patches may be submitted.** The instruction not to submit a change observed failing pulls against the always-submit rule, which is unchanged.
- **Temperature is nonzero.** Runs are not deterministic.
- **One baseline score does not establish variance.** A single new score cannot be separated from run-to-run noise.

## Result

- The Kaggle evaluation completed successfully.
- **Kaggle submission status**: Succeeded
- **Public Score**: 0.08
- **Baseline Public Score (Experiment 001)**: 0.06
- **Observed Delta**: +0.02

This is a positive observed signal. The observed Public Score increased from 0.06 to 0.08.

## Kaggle Submission Description

Experiment 002 - Evidence-Gated Verification

The SHA-256 of the bundle accepted by Kaggle and the submission timestamp were not recorded. The Kaggle notebook rebuilds the archive, so the accepted bundle may have a different hash from the local candidate `submission.zip`. The details in `experiment_card.json` under `candidate_bundle` describe the local candidate build only.

## Difference from Previous Experiment

- **Public Score delta**: +0.02 observed (0.06 to 0.08).
- **Behavioral difference**: the three-line verification change described under What Changed. Per-task results from the hidden evaluation are not available, so no behavioral observations are recorded.

## Observations

- Only the intended evidence-gated verification behavior changed.
- Model, architecture, analyzer, sampling, tools and localization strategy remained constant according to the experiment design.
- The observed Public Score increased from 0.06 to 0.08.
- Run-to-run variance remains unknown. Only one scored run exists for each experiment.
- Per-task results from the hidden evaluation are not available, so there are no observations on turn count, execution time, localization accuracy, failure modes or budget consumption.

## Decision

The result is consistent with the Experiment 002 hypothesis and is worth retaining as the current best measured configuration. Because run-to-run variance is unknown and only one scored run exists for each experiment, no strong causal conclusion is drawn from the +0.02 difference.

Experiment 002 is closed.

## Next Experiment

Not yet started.

The roadmap lists Adaptive Analyzer as the next planned candidate (Experiment 003). Its final design has not been selected or implemented.
