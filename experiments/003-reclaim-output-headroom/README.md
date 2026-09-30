# Experiment 003 — Reclaim Output Headroom

- **Experiment ID**: 003
- **Date prepared**: 2026-09-30

## Status

Prepared — not yet submitted to Kaggle.

## Control

- **Control experiment**: [002 — Evidence-Gated Verification](../002-evidence-gated-verification/)
- **Control Public Score**: 0.08 (one scored run)
- **Original baseline**: [001 — Baseline (Analyzer + Coder)](../001-baseline/), Public Score 0.06 (one scored run)

## Research Question

Does reducing `max_output_tokens` from 8192 to 4096 change the observed Kaggle Public Score when the rest of the Experiment 002 configuration remains unchanged?

## Observed Design Issue

Harness-source investigation indicates that `max_output_tokens` participates in the context-window request limit, so reserving 8192 output tokens may reduce available input headroom.

What is recorded in this repository:

- The Experiment 002 configuration sets `max_output_tokens: 8192` in `agent/configs/sampling.yaml`.
- The documented model context limit is 32,768 tokens.
- Both agents load the same file: `agent/agent.yaml` (line 5) and `agent/sub_agents/code_analyzer.yaml` (line 5) each `!include` `configs/sampling.yaml`.

What the source investigation indicates:

- The inference server rejects a request when input tokens plus requested output tokens exceed the context limit.
- The agent framework forwards `max_output_tokens` as the requested output tokens.
- A request rejected for this reason may end the task before a patch is submitted.

Under that reading, the Experiment 002 configuration may limit input headroom to approximately 32768 - 8192 = 24576 tokens.

Provenance and limits of this evidence:

- The investigation read a public mirror of the competition harness packages, together with the upstream source of the agent framework and the inference server.
- Exact equivalence between that source and the Kaggle scorer runtime is not verified.
- No run trajectories are available. How often the Experiment 002 agents approach this limit is unknown.
- This is an observed configuration mismatch. No claim is made that it caused any failed Kaggle task or the 0.08 score.

## Hypothesis

> If the output-token reserve is reduced from 8192 to 4096 while the rest of the Experiment 002 configuration remains unchanged, some long-running tasks may avoid fatal context overflow and reach patch submission.

This is a hypothesis, not an established Kaggle failure cause. It is not verified on hidden tasks.

## Independent Variable

`max_output_tokens`: 8192 → 4096

## What Changed

Only `agent/configs/sampling.yaml`. One existing line was modified. No lines, files, prompts or tools were added.

| Setting | Experiment 002 | Experiment 003 |
|:---|:---:|:---:|
| `max_output_tokens` | 8192 | 4096 |
| Implied input headroom (32768 minus the reserve) | approximately 24576 | approximately 28672 |
| `sampling.yaml` SHA-256 prefix | `ac857a0e8f76` | `048036d1c887` |

The value 4096 is half the control reserve. It is a single step chosen to test the mechanism, and it is not claimed to be optimal.

The exact diff can be regenerated from the repository root:

```bash
diff -u experiments/002-evidence-gated-verification/agent/configs/sampling.yaml experiments/003-reclaim-output-headroom/agent/configs/sampling.yaml
```

## What Stayed Constant

These four agent files are byte-for-byte identical to Experiment 002:

| File | SHA-256 |
|:---|:---|
| `agent/agent.yaml` | `c8f4305df8091c679985b610d3e5195e12703c5989f61230939a938ef6afb2e4` |
| `agent/prompts/system.md` | `614e4ead212287c07c40bc7f80834bd4f32fae4cd3b06b8224fc39f0c15a0e4b` |
| `agent/prompts/analyzer.md` | `3eb7a6eafba9f6d2d2d74b92822ed0715ebb6188202c41c53765b946392a02d5` |
| `agent/sub_agents/code_analyzer.yaml` | `07530d7f65c9654c3f074924cba10fb3e1fd22115885c3a948decc47fb055fc3` |

- **Architecture**: Analyzer + Coder, with `code_analyzer` invoked as an `agent_tool` (`skip_summarization: true`).
- **Model**: `gemma-4-31b-it-qat-w4a16-ct` for both agents.
- **Other sampling values**: `temperature` 0.2, `top_p` 0.95, `top_k` 40, `thinking_budget` 4096, `include_thoughts: false`. The other six lines of `sampling.yaml` are byte-identical.
- **Context limit**: 32768 tokens.
- **Tools**: the same tool lists for `swe_coder` and `code_analyzer`.
- **Prompts**: the coder prompt (including the Experiment 002 evidence-gated verification) and the analyzer prompt.

## Expected Mechanism

More input/context headroom at the cost of a smaller maximum single-response output.

## Expected Signal

A Public Score meaningfully above 0.08 would support further investigation of the hypothesis.

No numeric significance threshold is defined, because run-to-run variance is unknown.

## Negative / Uncertain Signal

- A Public Score at or below 0.08 may weaken the hypothesis.
- A very small change in either direction remains uncertain, because run-to-run variance is unknown.
- A neutral score may simply mean the changed context window rarely activated.

## Known Limitations

- **Run-to-run variance is unknown.** Temperature is nonzero, so runs are not deterministic.
- **Experiment 001 and 002 each have one scored run.** A single new score cannot be separated from run-to-run noise.
- **Actual overflow frequency for this agent is unknown.** No trajectories or per-task results are available.
- **Scorer equivalence is not verified.** Exact Kaggle scorer/runtime equivalence with the inspected harness source is not verified.
- **Long outputs may be truncated.** Reducing `max_output_tokens` may truncate unusually long model outputs, such as a large single file write.
- **Both agents change together.** `swe_coder` and `code_analyzer` share `sampling.yaml`, so the reserve changes for both. The result cannot be attributed to one agent.
- **A neutral score is ambiguous.** It may simply mean the changed context window rarely activated.

## Result

Kaggle Public Score: Pending

The submission description, bundle SHA256, submission time, score delta, observations and decision will be recorded after the official evaluation completes.

## Kaggle Submission Description

Pending. Nothing has been submitted. No submission archive has been built in this repository for Experiment 003. The contents of `agent/` are the source for the bundle.

## Difference from Previous Experiment

- **Public Score delta**: Pending.
- **Configuration difference**: the one-line `max_output_tokens` change described under What Changed. No behavioral observations exist yet.

## Observations

- Experiment prepared.
- No run has been executed, so there are no observations on turn count, execution time, context usage, failure modes or budget consumption.
- Result pending.

## Decision

Submit to Kaggle and wait for measurement before drawing conclusions.

## Next Experiment

Not yet decided.
