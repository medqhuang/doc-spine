# CLAUDE.md · widget-research

Auto-loaded. State lives in `instances/widget-v1/STATE.md` — read its frontmatter first, then act. Other docs on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/widget-v1/STATE.md` | Every session start |
| **Task spec** | `instances/widget-v1/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/widget-v1/reports/T<N>_report.md` | Task-relevant grep |
| **Strategy / stages / red lines** (founding frame) | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** | user-level memory | On demand |

## STATE writing discipline

- Event log is **append-only, one line per event** — format and examples live in the file's own header
- Findings enter the log as **claims, not facts**: key data carries its uncertainty; the consequence says what remains open or would overturn it
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) are pointers, overwritten in place; anything longer than a line belongs in the event log, a report, or `scratch/`
- History corrections go through `git revert` + a new event, never a "correction" paragraph

> *This section is a STATE.md special case (event log append-only writing is architecturally critical). Writing rules for other files belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules

1. **Status must not leak**: all live status -> `STATE.md`
2. **Demo scope**: extend freely; the goal is document structure, not widget science

## Commit boundary

Before any commit, `STATE.md` must reflect current truth: classify the session state (task still running / just completed / interim), update per [HOWTO Scenario D](../../HOWTO.md), and run its drift checks on the diff.

Commit policy: standard; commit logical units with a why-focused message.
