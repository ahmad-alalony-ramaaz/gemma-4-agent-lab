# Changelog

All notable updates and experimental milestones for this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Experiment 003 — Reclaim Output Headroom] - 2026-09-30

- Prepared Experiment 003 from the frozen Experiment 002 agent artifacts. Not yet submitted to Kaggle.
- Changed one primary configuration variable: `max_output_tokens` reduced from 8192 to 4096 (one line of `agent/configs/sampling.yaml`).
- Kept `agent.yaml`, `prompts/system.md`, `prompts/analyzer.md` and `sub_agents/code_analyzer.yaml` byte-for-byte identical to Experiment 002.
- Hypothesis: a harness-source investigation indicates that `max_output_tokens` participates in the context-window request limit, so a smaller output reserve may allow some long-running tasks to avoid context overflow and reach patch submission. This is not verified on hidden tasks.
- Refined the roadmap. Reclaim Output Headroom is now Experiment 003. Adaptive Analyzer, Progressive Localization and Reviewer Agent move to 004, 005 and 006 and remain preliminary candidates.
- Control: Experiment 002 (Public Score 0.08). Original baseline: Experiment 001 (0.06).
- Public Score: Pending.

## [Experiment 002 — Evidence-Gated Verification] - 2026-09-27

- Prepared Experiment 002 from the frozen Experiment 001 agent artifacts.
- Changed one primary behavioral variable: verification becomes evidence-gated (three lines of `agent/prompts/system.md`).
- Kept `agent.yaml`, `prompts/analyzer.md`, `sub_agents/code_analyzer.yaml` and `configs/sampling.yaml` byte-for-byte identical to Experiment 001.
- Refined the roadmap after the Experiment 001 diagnostic audit identified a stronger evidence-backed verification gap. Evidence-Gated Verification is now Experiment 002. Adaptive Analyzer, Progressive Localization and Reviewer Agent move to 003, 004 and 005. The earlier roadmap was a preliminary plan that was refined once evidence became available.
- Experiment 002 completed. The Kaggle evaluation succeeded.
- Public Score: 0.08.
- Experiment 001 baseline: 0.06.
- Observed delta: +0.02.
- The result is treated as a positive observed signal, not causal proof. Run-to-run variance is unknown.
- Experiment 002 is currently the highest observed score in the lab.

## [Experiment 001 — Baseline] - 2026-09-25

- Established initial Analyzer + Coder architecture.
- Added reproducible experiment metadata.
- Submitted baseline to Kaggle.
- The Kaggle evaluation completed successfully.
- Public Score: 0.06.
- This result is now the reference baseline for future controlled experiments.
