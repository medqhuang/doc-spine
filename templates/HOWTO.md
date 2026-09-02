# <PROJECT_NAME> · HOWTO

Local quick reference for using the doc-spine layout in this project.

## Where things go

| You have | Write it to |
|---|---|
| Status change / decision / reversal / event | `instances/<ACTIVE_INSTANCE>/STATE.md` |
| Task spec | `instances/<ACTIVE_INSTANCE>/docs/TASKS.md` |
| Completion report + data trace | `instances/<ACTIVE_INSTANCE>/reports/T<N>_report.md` |
| Strategy / stages + forks / red lines | `docs/ROADMAP.md` |
| Cross-project knowledge | user-level memory |
| Session wrap-up ("where things stand + what's next") | no single home — split it: in-flight → `in_flight_jobs`; done → events; hunch → one `hypothesis` / `question` line; next step → `next_action`, one line |

## Session start

1. Read `instances/<ACTIVE_INSTANCE>/STATE.md` frontmatter.
2. Skim the last 3 event-log entries.
3. Read `docs/ROADMAP.md` or `instances/<ACTIVE_INSTANCE>/docs/TASKS.md` only when the current task needs it.

## Task completion

1. Update `STATE.md` frontmatter.
2. Append one event-log line to `STATE.md`.
3. Create or update the relevant `reports/T<N>_report.md`.
4. (research / writing) Literature settle: from events after the last `lit·settled` + their `ref:` reports, extract new citations → spawn a settle subagent to add them into Zotero → append a `lit·settled` event; skip if none. See `docs/CONVENTIONS.md §10`.
5. Drift checks on the diff before committing (commands: canonical HOWTO, Scenario D): ≤3 files for this one event; no status prose added outside `STATE.md`; frontmatter agrees with the body; pointer fields still one line.
6. Commit the logical change.

