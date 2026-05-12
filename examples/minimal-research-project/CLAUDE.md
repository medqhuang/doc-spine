# CLAUDE.md · widget-research

Auto-loaded. Read this + `instances/widget-v1/STATE.md` frontmatter, then act. Other docs grep on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/widget-v1/STATE.md` (append one line, never a paragraph) | Every session start |
| **Task spec** | `instances/widget-v1/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/widget-v1/reports/T<N>_report.md` | Task-relevant grep |
| **Strategy / red lines / locked decisions** | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** | user-level memory | On demand |

## STATE writing discipline

- Event log is **append-only, one line per event**: `YYYY-MM-DD · scope · event type · summary + key data. ref: <pointer>`
- Scope examples: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural` (extend as needed)
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) overwrite in place, never prose
- History corrections go through `git revert`, never a "correction" paragraph

> *This section is a STATE.md special case (high-frequency append-only is architecturally critical). Writing rules for other files belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules

1. **Status must not leak**: all live status -> `STATE.md`
2. **Demo scope**: extend freely; the goal is document structure, not widget science

Commit policy: standard; commit logical units with a why-focused message.
