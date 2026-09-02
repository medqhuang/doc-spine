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
| Task spec (what it consumes; what counts as done or when to stop; the rest as needed) | `docs/TASKS.md` §the relevant task |
| Completion report + data trace | `reports/T<N>_report.md` (write once, read-only after) |
| Strategy / stages + forks / red lines (founding frame, near write-once) | `docs/ROADMAP.md` |
| Tooling quirks / cross-project knowledge | `~/.claude/projects/.../memory/` or equivalent |
| Pure registry (resource paths, asset inventory) | `docs/<name>.yaml` |
| Intermediate exploration draft (derivation, audit, reframe proposal, probe plan) | `instances/<active>/scratch/<topic>.md` — **non-canonical, must promote**; the event that used it carries the `ref:` (SPEC §8.4) |
| Citation / literature (research/writing) | Identity in **Zotero** + a `lit·settled` event in `STATE.md` (pointer + audit); settle at boundary — see CONVENTIONS §10 |
| Open question / untested conjecture the next session must carry | one `question` / `hypothesis` line in `STATE.md`, at the session boundary (SPEC §7.3) |
| Session wrap-up ("where things stand + what's next") | **No home — it mixes types.** In-flight → `in_flight_jobs`; done → event lines (if not already logged); hunch → one `hypothesis` / `question` line; derivation → `scratch/`; next step → `next_action`, one line |

**Default state: no 6th content type.** Add new locations only when a real signal demands them. Pivot-phase scratch (the last row) is a holding area, not a content type — see SPEC §8.4.

---

## 3. Four daily scenarios (cover 90% of use)

### Scenario A · Session start (1-minute orient)

1. Read `STATE.md` frontmatter (5 lines) → know `active_task` (the session focus) + `in_flight_jobs` + `next_action`. Then glance at the task graph for any other `in_flight` rows advancing in parallel.
2. Skim the last 3 event-log entries → know what the AI did in the previous session.
3. If something looks wrong → push back. Otherwise → let the AI proceed per `next_action`.

### Scenario B · Task completion (AI finishes a task)

**Trigger**: the user declares the task complete (an explicit task-boundary statement).

1. **Update `STATE.md` frontmatter**: set `active_task` to the next task, clear `in_flight_jobs`, update `next_action`.
2. **Append a single event-log line** to `STATE.md`:
   ```
   YYYY-MM-DD · task:T<N> milestone · <one-line summary + key data + JID>. ref: reports/T<N>_report.md
   ```
3. **Create `reports/T<N>_report.md`** with the full data + interpretation.
4. **(research / writing) Literature settle**: from the events after the last `lit·settled` + the reports they `ref:`, grep out new DOI/arXiv → settle subagent files them in Zotero → append a `lit·settled` event. Skip if none. See [`templates/CONVENTIONS.md` §10](templates/CONVENTIONS.md).
5. **Stage decision for non-doc files**: if the project's `Commit policy` excludes certain file types from AI's auto-stage (e.g., compute inputs, generated artifacts), list any modified files of that kind under the completed task's path and ask the user whether to include them in this commit. User confirmation lifts the exclusion for this commit only.
6. **`git commit`** (one logical change, message says **why**).

### Scenario C · Decision reversal (a prior choice is overturned)

1. **Append to `STATE.md` event log**:
   ```
   YYYY-MM-DD · decision_reversal · <X → Y>. reason: <evidence>. affected: <subinstances/tasks>. ref: <previous event id or doc>
   ```
2. **If the founding frame itself changed** (a red line, a stage boundary, the central bet) → edit `docs/ROADMAP.md` in place (rare). Ordinary decisions and their reversals need nothing beyond the event line — the log *is* the standing set.
3. **If spec changed** → edit the relevant `docs/TASKS.md §task` field in place.

### Scenario D · Session end / commit trigger

**Trigger**: the session is ending, or a commit is requested. Either way `STATE.md` must reflect current truth before the commit lands.

AI's first move: classify the session-end state into one of three sub-cases.

#### D.1 · Task still running (job queued / executing, not yet finished)
1. `STATE.md` frontmatter: write `in_flight_jobs` entries (JID + type + submitted time); set `next_action = "wait for JID X, then do Y"`.
2. Event log: append `YYYY-MM-DD · task:T<N> · action · submitted JID X (<type>). ref: sbatch/<name>.sbatch`
3. Do **not** flip `active_task` or task-graph status — the task is still in flight.
4. Commit per project policy.

#### D.2 · Task just completed
This is **Scenario B**. The commit is the natural end of that flow.

#### D.3 · Interim session — discussion / scoping / setup only (no job submitted, no task done)
1. `STATE.md` frontmatter: update `next_action` only — one line. If the wrap-up wants a paragraph, split it per §2's last row; check 4 below enforces this.
2. Event log: append a `discovery` or `action` line describing what the session advanced (a decision considered, a file restructured, a path scoped).
3. No task-graph change; no report.
4. Commit per project policy. Empty-result sessions still get one event line — the audit trail must explain the gap.

#### Before the commit lands (all three sub-cases)

Run the four commit-boundary checks of SPEC §10 on **this session's diff**, not the tree:

```bash
# 1 · locations-per-event: ≥4 files for one event → one of them is a second home; fix before committing
git diff HEAD --stat
# 2 · status prose leak, in every language the project writes in, outside STATE.md (scratch/ is exempt: non-canonical)
git diff HEAD -U0 -- . ':!*STATE.md' ':!*scratch/*' | grep -nE '^\+.*(SUPERSEDED|DEPRECATED|当前状态|in-progress|pending|已落|作废|[0-9]{4}-[0-9]{2}-[0-9]{2}[^|]{0,12}(update|更新|勘误|后记|修订))'
# 3 · frontmatter drift: if STATE frontmatter changed, do its values agree with the task graph and the newest event? (read, no command)
# 4 · pointer field became prose: a one-line pointer over ~250 characters, or a field spanning lines
git diff HEAD --name-only -- '*STATE.md' | while read -r f; do LC_ALL=en_US.UTF-8 grep -nHE '^(active_task|next_action|thesis):.{250,}' "$f"; done
```

Check 4 trips when a wrap-up has been packed into a pointer. Split it per §2's last row and leave the field a one-liner.

The remaining SPEC §10 checks examine accumulated state, not this change; they stay on-demand.

**Commit policy**: doc-spine itself does not auto-commit. Whether the commit-trigger signal also runs `git commit` is a project decision recorded in `CLAUDE.md` (see the template's `Commit policy:` line).

---

## 4. New project from scratch (~30-minute checklist)

```
[ ] Copy the templates with the Quickstart block in README.md (mkdir + one cp per file)
[ ] Fill placeholders (<PROJECT_NAME>, <ACTIVE_INSTANCE>, ...) — the list is in templates/README.template-pack
[ ] CLAUDE.md          <40 lines — 5 content types table + Hard rules + Commit policy
[ ] README.md          ≤30 lines — top-level layout + entry table
[ ] docs/ROADMAP.md    strategy + stages/forks + red lines (founding frame)
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
| A scratch / working draft treated as the single source (cited as "authority") | Scratch is non-canonical; promote the content to STATE / ROADMAP / report, leave a pointer (SPEC §8.4) |

Root causes and fixes for each: [SPEC §8-§9](SPEC.md).

