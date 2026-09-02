# CLAUDE.md · <PROJECT_NAME>

Auto-loaded. State lives in `instances/<ACTIVE_INSTANCE>/STATE.md` — read its frontmatter first, then act. Other docs on demand.

## 5 content types · single-source map

| Content | Lives in | When to read |
|---|---|---|
| **State / decisions / events / reversals** | `instances/<ACTIVE_INSTANCE>/STATE.md` | Every session start |
| **Task spec** (input / steps / output / criteria / risks / locked decisions) | `instances/<ACTIVE_INSTANCE>/docs/TASKS.md` §relevant task | Current task start |
| **Completion report + data trace** | `instances/<ACTIVE_INSTANCE>/reports/T<N>_report.md` (write once, then read-only) | Task-relevant grep |
| **Strategy / stages + forks / red lines** (founding frame, near write-once) | `docs/ROADMAP.md` | Decision points |
| **Cross-project knowledge** (tooling quirks, env gotchas) | `~/.claude/projects/<PROJECT_PATH>/memory/` | On demand |

## STATE writing discipline

- Event log is **append-only, one line per event** — format and scope / type examples live in the file's own header
- Findings enter the log as **claims, not facts**: key data carries its uncertainty; the consequence says what remains open or would overturn it
- Frontmatter fields (`active_task` / `in_flight_jobs` / `next_action`) are **pointers, overwritten in place**. A value that needs more than a line is not a pointer — it belongs in the event log, a report, or `scratch/`
- History corrections go through **`git revert`** + a new event, never a "correction" paragraph

> *This section is a STATE.md special case (event log append-only writing is architecturally critical). Writing rules for other files (TASKS / reports / ROADMAP) belong in `docs/CONVENTIONS.md`, not here.*

## Hard rules

1. **Status must not leak**: no dated "update / 更新 / 勘误" prose in TASKS / reports / sub-instance docs; all status → `STATE.md`
2. **Frozen instances** (`instances/<frozen>/`) are read-only by default (enforced by `.aiignore`); reorganization may move old md into `_legacy/`, never edit content
3. **<DOMAIN-SPECIFIC-RULE>**: e.g., for compute-heavy projects: "HPC large files (`*.save/`, `WAVECAR`, `*.wfc*`) do not rsync back to local"; replace with your domain's equivalent
4. **Literature via pointers** (research/writing): cite by DOI/arXiv, identity single-sourced in Zotero; settle at task completion, append a `lit·settled` event. See `docs/CONVENTIONS.md §10`.

## Commit boundary

Before any commit, `STATE.md` must reflect current truth: classify the session state (task still running / just completed / interim), update per [HOWTO Scenario D](HOWTO.md), and run its drift checks on the diff.

Commit policy: [define project authorization here, or leave default Claude Code git safety in place. If AI is authorized to commit autonomously but you want certain file types excluded from auto-stage (e.g., compute inputs, generated artifacts), name them here — AI will surface them at task completion per [HOWTO Scenario B](HOWTO.md).]
