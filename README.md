# Competence, Not Accuracy: Supplementary Code

Supplementary code for:

**Competence, Not Accuracy: A Diagnostic for Reference-Free Judge Gates in Skill Optimization**

This package is code-only. Raw datasets, generated rollouts, model responses,
checkpoints, figures, logs, and paper PDFs are intentionally excluded from the
submission archive. Experimental outputs are written under `outputs/` and
should be archived separately when needed for auditing.

## Code Map

- `skillopt/evaluation/judge.py`: reference-free judge prompt, response
  template, and score parser. Judge prompts receive question and answer
  content only; gold answers and hard labels are not provided to the judge.
- `skillopt/evaluation/gate.py`: verifier, judge, and random gate decision
  functions used by the closed-loop experiments.
- `skillopt/engine/trainer.py`: training-loop integration for verifier,
  judge, and random gate arms, including per-step judge/verifier logging,
  false-accept, and false-reject fields.
- `scripts/run_competence_diagnostic_matrix.py`: paper-facing entry point for
  the cross-model competence/discriminability matrix.
- `scripts/run_cross_provider_matrix.py`: implementation used by the matrix
  entry point, including row-level checkpointing and summary metrics.
- `scripts/analyze_auc.py`: offline marginal AUC, within-id AUC_w, confound
  share, and id-cluster bootstrap confidence intervals.
- `scripts/closed_solve.py`: closed-book self-solve measurement for judge
  competence, with gold/reference answers used only for offline scoring.
- `scripts/rejudge_episodes.py`: offline re-judging of stored rollout rows.
- `scripts/summarize_gate_experiment.py`: aggregation for closed-loop gate
  runs, including final verifier score, accept/reject counts, false-accept,
  false-reject, and judge-verifier divergence by step.
- `scripts/final_closed_solve_tables.py`: table aggregation for closed-book
  solve outputs.
- `scripts/plot_competence_discriminability.py` and
  `scripts/plot_gpqa_competence_attribution.py`: plotting scripts for appendix
  figures.

## Model Names

The matrix uses these model labels in output filenames:

| Paper label | API/model id used by code | Display name |
|---|---|---|
| `qwen-7b` | `qwen/qwen-2.5-7b-instruct` | Qwen2.5-7B-Instruct |
| `mimo-7b` | `xiaomi/mimo-v2.5` | Xiaomi MiMo-V2.5 |
| `deepseek-v4-flash` | `deepseek/deepseek-v4-flash` | DeepSeek-V4-Flash |
| `deepseek-8b` | not configured in this code snapshot | DeepSeek-R1-Distill-8B, if an exact provider route is supplied |

## Reproduction Commands

Run the reference-free judge discriminability and closed-book competence matrix:

```bash
export OPENROUTER_API_KEY=...
export OPENROUTER_BASE_URL=https://openrouter.ai/api/v1
python scripts/run_competence_diagnostic_matrix.py \
  --judges qwen-7b mimo-7b deepseek-v4-flash \
  --tasks math searchqa gpqa \
  --part both \
  --workers 1 \
  --retries 3 \
  --timeout 120 \
  --judge-max-tokens 16000 \
  --solve-max-tokens 16000 \
  --n-boot 2000 \
  --max-errors 5
```

Summarize already written rows without calling models:

```bash
python scripts/run_competence_diagnostic_matrix.py \
  --judges qwen-7b mimo-7b deepseek-v4-flash \
  --tasks math searchqa gpqa \
  --part summary \
  --n-boot 2000
```

Summarize closed-loop gate experiments:

```bash
python scripts/summarize_gate_experiment.py outputs/<run_dir_1> outputs/<run_dir_2>
```

## Output Policy

Do not commit:

- `outputs/`
- `data/` contents other than split metadata explicitly tracked by the base
  repository
- `ckpt/` contents other than `ckpt/README.md`
- `logs/`
- generated figures/images
- paper PDFs
- local virtual environments or external helper repositories

The `.gitignore` is configured for this code-only supplementary release.
