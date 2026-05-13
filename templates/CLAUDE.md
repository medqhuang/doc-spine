# CLAUDE.md · <PROJECT_NAME>

Auto-loaded. Read this + `instances/<ACTIVE_INSTANCE>/STATE.md` frontmatter (start with `active_task`; then scan the task graph for any other `in_flight` rows), then act. Other docs grep on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/<ACTIVE_INSTANCE>/STATE.md` (append one line, never a paragraph) | Every session start |
| **Task spec** (input/steps/output/criteria/risks/locked decisions) | `instances/<ACTIVE_INSTANCE>/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/<ACTIVE_INSTANCE>/reports/T<N>_report.md` (write once, then read-only) | Task-relevant grep |
| **Strategy / red lines / locked decisions / DAG** | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** (tooling quirks, env gotchas) | `~/.claude/projects/<PROJECT_PATH>/memory/` | On demand |

## STATE writing discipline

- Event log is **append-only, one line per event**: `YYYY-MM-DD · scope · event type · summary + key data (JID) + consequence. ref: <pointer>`
- Scope examples: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural` (extend as needed)
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) overwrite in place — never prose
- History corrections go through **`git revert`**, never a "correction" paragraph in the document

> *This section is a STATE.md special case (event log append-only writing is architecturally critical). Writing rules for other files (TASKS / reports / ROADMAP) belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules

1. **Status must not leak**: no `2026-XX-XX update` prose in TASKS / reports / sub-instance docs; all status → `STATE.md`
2. **Frozen instances** (`instances/<frozen>/`) are read-only by default (enforced by `.aiignore`); reorganization may move old md into `_legacy/`, never edit content
3. **<DOMAIN-SPECIFIC-RULE>**: e.g., for compute-heavy projects: "HPC large files (`*.save/`, `WAVECAR`, `*.wfc*`) do not rsync back to local"; replace with your domain's equivalent

## Session end response

When the user signals session end ("收工" / "wrap up" / "结束") **or** asks to commit, ensure `STATE.md` reflects current truth before committing — classify the state (task still running / just completed / interim) and update per [HOWTO Scenario D](HOWTO.md).

Commit policy: [define project authorization here, or leave default Claude Code git safety in place.]
