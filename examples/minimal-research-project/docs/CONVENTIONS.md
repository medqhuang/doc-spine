# widget-research · Conventions

> Minimal version for this demo. Real projects expand on this template — see [../../../templates/CONVENTIONS.md](../../../templates/CONVENTIONS.md).

## 5 content types · single-source map

| Content | Location |
|---|---|
| State / decisions / events | `instances/widget-v1/STATE.md` |
| Task spec | `instances/widget-v1/docs/TASKS.md` |
| Completion reports | `instances/widget-v1/reports/T<N>_report.md` |
| Strategy / red lines | `docs/ROADMAP.md` |
| Cross-project knowledge | user-level memory |

## File naming

- Task reports: `T1_report.md`, `T2_report.md`, ...
- Sub-instance (would normally be here): `widget-v1/01_measure/<run_id>/`

## Git workflow

- Commit message says **why**, not what
- No force push to main
- Failed pre-commit hook → fix and create a new commit (do NOT amend)
