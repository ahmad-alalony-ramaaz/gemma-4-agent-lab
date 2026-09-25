# Experimentation Methodology & Protocol

This document defines the scientific process and documentation standards for all experiments conducted in `gemma-4-agent-lab`.

---

## 1. Principles of Controlled Agent Experimentation

When developing autonomous coding agents, the space of possible modifications is vast: system prompt wording, few-shot examples, tool schemas, sub-agent decomposition, context truncation strategies, sampling temperature, and thinking token budgets.

### The Single-Variable Rule
**Every experiment must mutate exactly one primary variable at a time.**

If an experiment simultaneously alters the system prompt instructions, adjusts sampling temperature, and introduces a new sub-agent, it becomes impossible to determine:
- Which specific change improved or degraded performance?
- Did one modification counteract the benefits of another?
- What are the true token or compute cost trade-offs of the individual components?

By restricting each iteration to a single primary change, we establish direct causality between architectural/prompt modifications and public benchmark results.

---

## 2. Standard Experiment Record Template

Every experiment documented in `experiments/<id>-<name>/README.md` must include the following standardized sections:

```markdown
# Experiment [ID] — [Name]

- **Experiment ID**: e.g., 002
- **Date**: YYYY-MM-DD
- **Hypothesis**: Precise statement predicting what will happen and why.
- **Single Primary Change**: Explicit description of the one variable modified.
- **Files Changed**: List of files created or edited.
- **Configuration**:
  - Model alias
  - Context limit
  - Sampling parameters (temperature, top_p, top_k, max_output_tokens, thinking_budget)
  - Tool availability
- **Kaggle Submission Description**: Description text and bundle SHA256 submitted to Kaggle.
- **Public Score**: Exact score returned by Kaggle (or "Pending" while running).
- **Difference from Previous Experiment**: Direct delta in public score and qualitative behavioral shifts.
- **Observations**:
  - Turn count and execution time trends
  - Localization accuracy
  - Common failure modes or error logs
  - Budget/token consumption
- **Decision**: Adopt, iterate, or discard/revert.
- **Next Experiment**: Next logical single-variable hypothesis.
```

---

## 3. Experiment Metadata Card (`experiment_card.json`)

Alongside the human-readable `README.md`, each experiment folder must contain an `experiment_card.json` file capturing machine-readable metadata:

```json
{
  "created_utc": "YYYY-MM-DDTHH:MM:SS+00:00",
  "zip": "submission.zip",
  "sha256": "<SHA-256 of submission archive>",
  "architecture": "<architecture label>",
  "bundle_base": "<baseline bundle reference>",
  "model_alias": "<exact model identifier>",
  "tools": ["<list>", "<of>", "<tools>"],
  "context_limit": 32768,
  "sampling": {
    "temperature": 0.2,
    "top_p": 0.95,
    "top_k": 40,
    "max_output_tokens": 8192,
    "thinking_budget": 4096
  },
  "prompt_sha": "<sha256 prefix of primary system prompt>",
  "validation_passed": true
}
```

---

## 4. Lifecycle of an Experiment

1. **Formulate Hypothesis**: Identify a recurring bottleneck from prior results (e.g., inaccurate localization, context saturation, premature patch submission).
2. **Implement Isolated Modification**: Implement the single change in a dedicated experiment directory (`experiments/<XXX>-<name>/`).
3. **Verify Bundle Integrity**: Run local schema validation, verify bundle references, and record SHA256 checksums.
4. **Submit to Kaggle**: Upload the submission archive to the competition evaluation harness.
5. **Log Pending State**: Commit the experiment documentation with status `Pending` (never invent benchmark numbers).
6. **Record Evaluation Outcome**: Once official scoring completes, update the score, analyze diffs, record observations, and decide whether to promote the change into the baseline for future experiments.
