# CLAUDE.md · <PROJECT_NAME>

Auto-loaded. Read this + `instances/<ACTIVE_INSTANCE>/STATE.md` frontmatter, then act. Other docs grep on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/<ACTIVE_INSTANCE>/STATE.md` (append one line, never a paragraph) | Every session start |
| **Task spec** (input/steps/output/criteria/risks/locked decisions) | `instances/<ACTIVE_INSTANCE>/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/<ACTIVE_INSTANCE>/reports/T<N>_report.md` (write once, then read-only) | Task-relevant grep |
| **Strategy / red lines / locked decisions / DAG** | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** (tooling quirks, env gotchas) | `~/.claude/projects/<PROJECT_PATH>/memory/` | On demand |

Archive semantics: `docs/_archive/` = project-level historical snapshots; `instances/<frozen>/_legacy/` = byte-level retention inside frozen instances. Do not mix.

## STATE writing discipline

- Event log is **append-only, one line per event**: `YYYY-MM-DD · scope · event type · summary + key data (JID) + consequence. ref: <pointer>`
- Scope examples: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural` (extend as needed)
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) overwrite in place — never prose
- History corrections go through **`git revert`**, never a "correction" paragraph in the document

> *This section is a STATE.md special case (high-frequency append-only is architecturally critical). Writing rules for other files (TASKS / reports / ROADMAP) belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules

1. **Status must not leak**: no `2026-XX-XX update` prose in TASKS / reports / sub-instance docs; all status → `STATE.md`
2. **Frozen instances** (`instances/<frozen>/`) are read-only by default (enforced by `.aiignore`); reorganization may move old md into `_legacy/`, never edit content
3. **<DOMAIN-SPECIFIC-RULE>**: e.g., for compute-heavy projects: "HPC large files (`*.save/`, `WAVECAR`, `*.wfc*`) do not rsync back to local"; replace with your domain's equivalent

## Commit (project-specific authorization, optional)

[Define your project's commit policy here. Example: "After completing a logical unit of work, you may `git commit` directly without explicit ask." Otherwise default Claude Code git safety applies.]
