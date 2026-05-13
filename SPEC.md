# SPEC · doc-spine

> Formal specification (the **why**) of doc-spine, an anti-entropy documentation architecture. For day-to-day operations, see [HOWTO.md](HOWTO.md).

English (Chinese translation: contributions welcome)

---

## 1. The core problem

Documentation systems fail not because documents are too many, but because **entropy accumulates**. Two carriers:

1. **Duplication** — the same information rewritten in prose across multiple files
2. **Accumulation** — status changes appended in multiple files as new prose paragraphs

Neither causes immediate failure. Both compound silently. After a few months, the project becomes unreadable — not because content is wrong, but because **no single source can be trusted as current**.

This specification provides three coupled mechanisms to prevent both carriers.

---

## 2. Three anti-entropy mechanisms

### 2.1 Single Source

Each content type has exactly **one** canonical location. Other files may **reference** the location with a pointer, but **must not duplicate** the content as prose.

If you find yourself writing the same status update in two files, one of them is wrong by definition. The architecture says: pick one, delete the other, replace with a pointer.

### 2.2 One-Way ownership

Cross-file **ownership and write flow** are one-way: downstream files may read upstream files, but downstream files do not mirror upstream state in their own prose. Navigational links may point both directions; duplicated ownership may not.

For example: `STATE.md` is upstream of `TASKS.md` (task graph derives from state). `TASKS.md` should not have a "current status" section that mirrors `STATE.md`'s frontmatter. If `TASKS.md` needs to show status, it points to `STATE.md`.

### 2.3 Append-Only event log

**Scope**: append-only applies to the **event log inside `STATE.md`**, not the whole file. Frontmatter (current snapshot) and task graph (status aggregation) are **overwritten** on update — only the event log is the immutable audit trail.

State changes, decisions, and reversals are recorded as **append-only events** in the event log. Past entries are not edited.

When a previous decision is reversed, you do NOT:
- Edit the old entry to add `(SUPERSEDED)` annotation
- Add a "correction" paragraph to the report header
- Modify the old entry's content

You DO:
- Append a new event describing the reversal (with `prior_decision` reference)
- If the old entry was factually wrong (not just superseded), use `git revert` + new event

This makes the audit trail trustworthy and trivially searchable.

---

## 3. Five content types and their locations

| # | Content type | Update frequency | Audience | Canonical location | Format |
|---|---|---|---|---|---|
| 1 | **State + events** (status, decisions, reversals, milestones) | High (every event) | AI continuous update; human reviews at boundaries | `<instance>/STATE.md` | MD + YAML frontmatter |
| 2 | **Task spec** (input, steps, output, criteria, risks, locked decisions) | Medium (when spec actually changes) | AI + human co-read | `<instance>/docs/TASKS.md` | MD |
| 3 | **Completion report + data trace** (results, physics interpretation, reproducibility info) | Medium (write once at task completion, then read-only) | Human-primary, AI references | `<instance>/reports/T<N>_report.md` | MD |
| 4 | **Strategy + red lines + locked decisions + DAG** | Low (decision-driven) | Human-primary; AI auto-respects | `docs/ROADMAP.md` | MD |
| 5 | **Cross-project knowledge** (tooling quirks, compute env gotchas) | Low | AI across sessions | `~/.claude/projects/.../memory/` or equivalent persistent memory | MD |

There is also an implicit **type 6: pure registry** (resource inventories, asset paths) — use YAML when it appears, but in a typical research project this is rare enough not to require a default location.

**The absence of a 6th type should be the default state.** Adding new types only when real demand emerges (e.g., a paper-writing phase that needs a canonical numbers ledger) is the architecture working as intended.

---

## 4. Design principles

§2-§3 encode the structural rules (single source, one-way ownership, append-only, state/spec separation). The principles below are the governance choices layered on top:

1. **Format follows content nature, not audience.** Markdown is the default; YAML for pure registries / structured records; YAML frontmatter for high-frequency structured fields embedded in prose. Don't pick format because "AI reads it" or "humans read it" — both can handle either.

2. **Responsibility split ≠ file split.** AI and human read the same set of files at different cadences. AI continuously updates state; human reviews at three boundaries (session start, task completion, decision point). The architecture does not need separate "AI files" and "human files."

3. **Boundaries enforced by mechanism, not discipline.** Where possible, use mechanisms (e.g., `.aiignore` files for read-only directories) rather than relying on humans or AI to remember rules.

4. **Simple enough to maintain without discipline.** If the architecture requires constant discipline to follow, it will fail after a few reversals. The architecture must be simple enough that the path of least resistance is also the correct path.

---

## 5. File layout reference (lean starter)

```
<project>/
├── CLAUDE.md ⭐               ≤30 lines — 5-content-types table + Hard rules + Commit policy
├── README.md                  ≤30 lines — top-level layout + entry table
├── HOWTO.md                   (optional) — local quick-reference for the architecture
├── docs/
│   ├── ROADMAP.md             strategy / stages / red lines / DAG / locked decisions
│   ├── CONVENTIONS.md         naming / git workflow / frozen boundaries
│   └── _archive/              historical snapshots (move during reorg, do not delete)
├── instances/
│   ├── <frozen_baseline>/     .aiignore enforces read-only
│   └── <active>/
│       ├── STATE.md ⭐⭐      YAML frontmatter (current state) + Task graph + Event log + Sub-instance roster
│       ├── README.md          instance entry + stage directories + historical doc topic navigation
│       ├── docs/TASKS.md      T1-TN spec (pure spec, no status)
│       ├── reports/T<N>_report.md   completion reports + data trace
│       └── <stage_NN>/<sub-instance>/  inputs / sbatch / scripts / structures + meta/README
```

The `⭐` markers denote the two files most critical to architectural integrity: `CLAUDE.md` (the entry rules) and `STATE.md` (the single source of state).

---

## 6. Anti-entropy flow (event-triggered update path)

When an event occurs (job completes, decision is made, status changes), the update path follows a strict single-direction flow:

```
        ┌────── User triggers event / AI completes task ──────┐
        │                                                      │
        ▼                                                      │
   STATE.md ← (single source of truth for status)              │
     │  Event appended; frontmatter fields updated             │
     │                                                          │
     ├──→ ROADMAP.md (only if new strategic decision; rare)     │
     ├──→ TASKS.md (only if spec actually changed)              │
     ├──→ reports/T<N>_report.md (only if a task completed)     │
     │                                                          │
     │  All other files: untouched                              │
     └──────────────────────────────────────────────────────────┘
                       ↓
       One reversal = 1-3 location updates
       (vs. legacy architecture: 6+ prose sync points)
```

The 1-3 threshold is also the audit signal — see [§10 Drift detection](#10-drift-detection).

**Triggering events** include: a job completing, a decision being made, a discovery surfacing — **and the session boundary itself** (user signals "wrap up" / requests a commit). The session boundary is a triggering event because, regardless of whether the underlying task finished, the audit trail must reflect what advanced before the commit lands. See [HOWTO Scenario D](HOWTO.md) for the session-boundary sub-cases.

---

## 7. STATE.md anatomy

This is the file that does most of the heavy lifting. Detailed structure:

### 7.1 YAML frontmatter (current state — overwrites on update)

Minimum viable frontmatter is just two focus pointers:

```yaml
---
active_task: <task_id>
active_subinstance: <relative_path>
---
```

`active_task` is a **focus pointer**, not the full set of in-flight tasks. It answers "where should the next session start reading?" — a single session entry point. When multiple tasks advance in parallel, that fact is expressed by the task graph (§7.2), where each row carries its own `in_flight` / `blocked-by` status. Keeping `active_task` singular avoids duplicating the in-flight set in two places and forces an explicit "what to advance first" choice at session end.

**Reading order on session start**: read `active_task` first, then scan the task graph for any other `in_flight` rows that also need attention this session.

The fields below are **optional** — add them only when the truth doesn't already live elsewhere. **Omit `in_flight_jobs` / `next_action` / `last_updated` if the truth lives in an external system (SLURM queue, CI status, git mtime) or in the event log itself**; field-izing them duplicates state that will go stale and force reconciliation cost.

```yaml
---
stage: <stage_identifier>
active_task: <task_id>
active_subinstance: <relative_path>
in_flight_jobs:
  - {jid: <id>, type: <description>, depends_on: <id_or_null>}
next_action: "<concrete next step description>"
last_updated: <YYYY-MM-DD>
---
```

This block is **overwritten** on every update; it represents the current snapshot.

### 7.2 Task graph (semi-stable)

A table showing each task's status (`done` / `in_flight` / `blocked-by`) and a one-line description. **Overwritten** when tasks transition states — this is a derived view of the event log, kept inline for fast scanning.

### 7.3 Event log (append-only)

Each event is one line:
```
- YYYY-MM-DD · <scope> · <event type> · <one-line description with key data> [refs]
```

Scope examples (NOT a closed enum — extend as needed):
- `project` — project-wide event
- `task:T<N>` — task-specific event
- `subinstance:<path>` — sub-instance event

Event types (also open enum):
- `milestone` — task or stage completed
- `action` — significant action taken (job submitted, file moved)
- `discovery` — new finding that affects downstream
- `decision_reversal` — prior decision overturned
- `structural` — reorganization / file move

### 7.4 Sub-instance roster (semi-stable)

A table mapping `path → role → status`. Detailed configuration lives in the sub-instance itself (`inputs/`, `sbatch/`, `STATUS.yaml` if present), not in this roster.

---

## 8. Failure modes (observed in real projects)

Not hypothetical. Each has occurred and triggered a structural fix.

### 8.1 Project becomes uncontrollable

**Symptom**: 150 markdown files accumulated; prose status drifts across files; no one can tell which version is current.

**Root cause**: No single-source discipline. Status updates accumulated as new paragraphs in 6+ files in parallel.

**Fix**: Introduce `STATE.md` as the single status source. Strip status prose from all other files. (Migration: see [HOWTO.md §5](HOWTO.md).)

### 8.2 Sub-instance documentation goes stale silently

**Symptom**: A sub-instance's `CLAUDE.md` / `meta/README.md` still says "waiting for upstream X to complete," but X has been complete for weeks; the sub-instance has been running production for days.

**Root cause**: Status prose leaked into sub-instance docs. The sub-instance's local description was treated as a place to record current state, instead of pointing to the central `STATE.md`.

**Fix**: Sub-instance docs become **stable architecture descriptions only** (role, sbatch chain, interfaces). Status fields move to `STATE.md §sub-instance roster` or a sub-instance-local `STATUS.yaml`. Sub-instance prose never mentions current status — it points upward.

### 8.3 Entry file develops inter-section redundancy

**Symptom**: `CLAUDE.md` has §Read order + §Project facts + §5 content types table — all three sections describe the same content-to-location mapping in different framings.

**Root cause**: When solving "cross-file duplication" (the primary architecture goal), there is a natural instinct to add **redundancy within a single file** to make it "comprehensive." This violates the architecture's own principle at a sub-file level.

**Why this is the most important failure**: The anti-entropy principle must be **applied recursively** — across files **and within files**. A 30-line entry doc with three overlapping sections is just as broken as 6 files with overlapping prose.

**Fix**: Audit entry files for inter-section duplication. Each section must answer a question the others don't answer.

---

## 9. Anti-bloat patterns (preserve evolution space)

Micro-patterns that prevent the architecture from rigidifying as it evolves.

### 9.1 Don't harden enums

**Symptom**: Listing categories with words like "dictionary," "allowed values," or visually closed enum-like tables. Future scenarios then can't introduce a new category without feeling like a violation; the AI awkwardly forces new content into one of the existing categories.

**Cause**: Premature crystallization. The author at time T wrote down what categories existed at time T and unconsciously treated the list as closed.

**Fix**: Replace "dictionary" or "enum" with **"examples — extend as needed."** Make it explicit that the list is open.

**Example**: In `STATE.md`, the scope field used to read "scope dictionary: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural`". This subtly forbade new scope types like `paper_draft` or `reviewer_response`. Changed to "scope examples: ... (extend as needed)."

### 9.2 Precedent boundary annotations

**Symptom**: A special-case rule is added to an entry file (e.g., "STATE.md has its own writing discipline"). Six months later, someone reasons: "If STATE has special rules in the entry file, why not TASKS? Why not reports? Why not ROADMAP?" The entry file balloons.

**Cause**: The special-case rule was added without an explicit boundary marker. Future contributors (human or AI) assume the precedent applies generally.

**Fix**: When adding a special-case rule, **end the section with a one-line blockquote** stating: "This section is a special case for X (reason: Y). Similar rules for other files belong in CONVENTIONS.md, not here."

**Example**: In `CLAUDE.md`, after the §STATE writing discipline section:
> *This section is a STATE.md special case (event log append-only writing is architecturally critical). Writing details for other files (TASKS / reports / ROADMAP) belong in CONVENTIONS.md, not here.*

**Wider lesson — missing boundary > missing rule**: In an evolving architecture, the most dangerous omission is not a missing rule, it is a missing boundary. A special-case rule without an explicit boundary will, with high probability, be generalized by future contributors. Every special-case rule should carry its own fence; the fence costs one line, missing it costs a refactor.

### 9.3 When NOT to add a new pattern

Closing constraint for this section: do not add a new pattern, rule, audit script, or template until a real failure has occurred. The patterns above came from observed failures, not speculation. Speculative patterns themselves become bloat.

If you find yourself wanting to add a pattern preemptively, write it down in a personal scratch file. Wait for the failure. If it doesn't occur within 3 months of active use, the pattern was likely not needed.

---

## 10. Drift detection (periodic checks)

Low effort, high value:

- **Inter-section redundancy in entry files** — read `CLAUDE.md` / `README.md` top to bottom; ask "does §B say anything §A didn't?" If §B is a reframing of §A, merge or remove.
- **Status prose leak** — grep for `in-progress` / `pending` / `2026-XX-XX update` / `SUPERSEDED` / `当前状态` across all files. Any hit outside `STATE.md` / `STATE.yaml` is a leak.
- **Locations-per-event** — when an event occurs (job complete, reversal, status change), count files you update. Healthy ≤3; drift signal ≥4.
- **Frontmatter drift** — parse YAML frontmatter periodically; verify field values match the prose body. Frontmatter says `active_task: T2` but body still narrates T1 = silent drift.

---

## 11. Final philosophy

> **Each event is written once; one writing lands in one location; one location carries one content type.**

Every rule in this specification derives from these three clauses. If you ever need to extend the architecture, derive your extension from these clauses — do not add new principles.

---

**Implementation guidance**: see [HOWTO.md](HOWTO.md) for day-to-day operations, and [case-studies/](case-studies/) for two real-world projects (one failure, one success) demonstrating the architecture.
