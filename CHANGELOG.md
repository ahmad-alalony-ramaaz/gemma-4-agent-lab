# Changelog

All notable updates and experimental milestones for this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

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
