# CLAUDE.md · widget-research

Auto-loaded. Read this + `instances/widget-v1/STATE.md` frontmatter, then act. Other docs grep on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/widget-v1/STATE.md` (append one line, never a paragraph) | Every session start |
| **Task spec** | `instances/widget-v1/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/widget-v1/reports/T<N>_report.md` | Task-relevant grep |
| **Strategy / red lines / locked decisions** | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** | (your local user-memory directory) | On demand |

## STATE writing discipline

- Event log is **append-only, one line per event**: `YYYY-MM-DD · scope · event type · summary + key data. ref: <pointer>`
- Scope examples: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural` (extend as needed)
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) overwrite in place — never prose
- History corrections go through `git revert`, never a "correction" paragraph

> *This section is a STATE.md special case (high-frequency append-only is architecturally critical). Writing rules for other files belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules (this minimal example)

1. **Status must not leak**: all status → `STATE.md`
2. **This is a demo project**: feel free to extend / modify; the goal is to show the architecture in action, not perfect any specific widget

## Commit policy

Standard. After completing a logical unit of work, commit with a clear `why`-focused message.
