# CLAUDE.md · widget-research

Auto-loaded. Read this + `instances/widget-v1/STATE.md` frontmatter (start with `active_task`; then scan the task graph for any other `in_flight` rows), then act. Other docs grep on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/widget-v1/STATE.md` (append one line, never a paragraph) | Every session start |
| **Task spec** | `instances/widget-v1/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/widget-v1/reports/T<N>_report.md` | Task-relevant grep |
| **Strategy / stages / red lines** (founding frame) | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** | user-level memory | On demand |

## STATE writing discipline

- Event log is **append-only, one line per event**: `YYYY-MM-DD · scope · event type · summary + key data. ref: <pointer>`
- Scope examples: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural` (extend as needed)
- Scientific findings enter the log as **claims, not facts**: key data carries its uncertainty; consequence notes what remains open / would overturn it. Still one line
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) overwrite in place, never prose
- History corrections go through `git revert`, never a "correction" paragraph

> *This section is a STATE.md special case (event log append-only writing is architecturally critical). Writing rules for other files belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules

1. **Status must not leak**: all live status -> `STATE.md`
2. **Demo scope**: extend freely; the goal is document structure, not widget science

## Session end response

When the user signals session end ("收工" / "wrap up" / "结束") **or** asks to commit, ensure `STATE.md` reflects current truth before committing — classify the state (task still running / just completed / interim) and update per [HOWTO Scenario D](../../HOWTO.md).

Commit policy: standard; commit logical units with a why-focused message.
