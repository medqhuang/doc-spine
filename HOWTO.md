# HOWTO · Operational Guide

> How to use this architecture day-to-day. For **why** it works + known failure modes + anti-bloat patterns, see [SPEC.md](SPEC.md).

---

## 1. Is this for you?

Use this if your project is multi-session, multi-month, AI-collaborative, with possible decision reversals. Plain README + git log suffices for simpler workflows.

---

## 2. "I have X — where does it go?" (lookup table)

| You have | Write it to |
|---|---|
| Status change / decision / reversal / task completion / event | `STATE.md` (append **one line**, never a paragraph) |
| Task spec (input/steps/output/criteria/risks/locked decisions) | `docs/TASKS.md` §the relevant task |
| Completion report + data trace | `reports/T<N>_report.md` (write once, read-only after) |
| Strategy / red lines / locked decisions / DAG | `docs/ROADMAP.md` |
| Tooling quirks / cross-project knowledge | `~/.claude/projects/.../memory/` or equivalent |
| Pure registry (resource paths, asset inventory) | `docs/<name>.yaml` |

**Default state: no 6th content type.** Add new locations only when a real signal demands them.

---

## 3. Three daily scenarios (cover 90% of use)

### Scenario A · Session start (1-minute orient)

1. Read `STATE.md` frontmatter (5 lines) → know `active_task` + `in_flight_jobs` + `next_action`.
2. Skim the last 3 event-log entries → know what the AI did in the previous session.
3. If something looks wrong → push back. Otherwise → let the AI proceed per `next_action`.

### Scenario B · Task completion (AI finishes a task)

1. **Update `STATE.md` frontmatter**: set `active_task` to the next task, clear `in_flight_jobs`, update `next_action`.
2. **Append a single event-log line** to `STATE.md`:
   ```
   YYYY-MM-DD · task:T<N> milestone · <one-line summary + key data + JID>. ref: reports/T<N>_report.md
   ```
3. **Create `reports/T<N>_report.md`** with the full data + interpretation.
4. **`git commit`** (one logical change, message says **why**).
5. **Do NOT** add an "update" paragraph to `TASKS.md` or the old report header.

### Scenario C · Decision reversal (a prior choice is overturned)

1. **Append to `STATE.md` event log**:
   ```
   YYYY-MM-DD · decision_reversal · <X → Y>. reason: <evidence>. affected: <subinstances/tasks>. ref: <previous event id or doc>
   ```
2. **If strategy changed** → update `docs/ROADMAP.md §locked-decisions` (rare).
3. **If spec changed** → edit the relevant `docs/TASKS.md §task` field in place.
4. **Do NOT** modify `reports/` (data trace is frozen).
5. **Do NOT** add a `SUPERSEDED` banner anywhere (the event log is the audit trail).

---

## 4. New project from scratch (~30-minute checklist)

```
[ ] mkdir <project> && cd <project> && git init
[ ] mkdir -p docs instances/<active>/docs instances/<active>/reports
[ ] Copy templates explicitly:
    - templates/CLAUDE.md       → CLAUDE.md
    - templates/README.md       → README.md
    - templates/HOWTO.md        → HOWTO.md
    - templates/ROADMAP.md      → docs/ROADMAP.md
    - templates/CONVENTIONS.md  → docs/CONVENTIONS.md
    - templates/STATE.md        → instances/<active>/STATE.md
    - templates/INSTANCE_README.md → instances/<active>/README.md
    - templates/TASKS.md        → instances/<active>/docs/TASKS.md
[ ] Fill placeholders (<PROJECT_NAME>, <ACTIVE_INSTANCE>, etc.)
[ ] CLAUDE.md          ≤30 lines — 5 content types table + Hard rules + Commit policy
[ ] README.md          ≤30 lines — top-level layout + entry table
[ ] docs/ROADMAP.md    strategy + red lines + DAG skeleton
[ ] docs/CONVENTIONS.md  naming + git workflow + frozen boundaries
[ ] instances/<active>/STATE.md         YAML frontmatter + task graph + event-log scaffold
[ ] instances/<active>/docs/TASKS.md    T1-TN spec only (no status)
[ ] instances/<active>/reports/         empty directory; reports fill in as tasks complete
[ ] (optional) instances/<frozen>/.aiignore  for frozen baseline instances
[ ] git add -A && git commit -m "Bootstrap with doc-spine"
```

---

## 5. Migrating an existing project (1-2 hours)

If you're inheriting or recovering a project that has accumulated prose status across multiple files:

```
[ ] Create STATE.md from scratch. From TASKS.md / report headers / sub-instance prose, extract status fields → frontmatter, and historical events → event log.
[ ] TASKS.md: delete §current-status sections + per-task §in-progress / §known-pitfalls / §reversal narrative. Keep pure spec only.
[ ] reports/T<N>_report.md: collapse stacked SUPERSEDED banners into a clean frontmatter.
[ ] Top-level routing docs (file indexes, navigation manuals): archive to docs/_archive/.
[ ] Cross-project knowledge (env quirks): extract into ~/.claude/projects/.../memory/ or equivalent.
[ ] Add .aiignore to frozen / read-only instances.
[ ] Validate: grep for stale links + cross-reference consistency + commit the structural change as one logical commit.
```

---

## 6. Common errors (stop when you see these)

| Wrong | Right |
|---|---|
| Status prose duplicated across files (TASKS / reports / sub-instance docs) | All status → `STATE.md`; other files use pointers |
| Editing past entries (`SUPERSEDED` banner, "correction" paragraph) | New event in STATE log; `git revert` if factually wrong |
| Closed-enum category list ("dictionary of allowed scopes") | "Examples + extend as needed" |
| New CLAUDE.md rule without a boundary marker | If special case, append blockquote: "Special case for X; similar rules → CONVENTIONS" |

Root causes and fixes for each: [SPEC §8-§9](SPEC.md).

---

## 7. Practice path

Walk through scenarios A / B / C with a real event in your project. On the first try, it feels unnatural — you'll want to add prose to TASKS. After 3-5 repetitions, muscle memory takes over. The architecture is designed to be simpler than the alternative once internalized, not harder.
