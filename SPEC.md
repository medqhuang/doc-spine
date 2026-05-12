# SPEC · Anti-Entropy Documentation Architecture

> Formal specification of the architecture. **Why** it works. For **how** to use it day-to-day, see [HOWTO.md](HOWTO.md).

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

### 2.2 One-Way

Cross-file references are **one-way only**: downstream files read upstream files; downstream files do not write back into upstream files' prose.

For example: `STATE.md` is upstream of `TASKS.md` (task graph derives from state). `TASKS.md` should not have a "current status" section that mirrors `STATE.md`'s frontmatter. If `TASKS.md` needs to show status, it points to `STATE.md`.

### 2.3 Append-Only (event log discipline)

State changes, decisions, and reversals are recorded as **append-only events**. Past entries are not edited.

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

**The absence of a 6th type should be the default state.** Adding new types only when real demand emerges (e.g., a paper writing phase that needs a canonical numbers ledger) is the architecture working as intended.

---

## 4. Seven design principles

1. **Format follows content nature, not audience.** Markdown is the default; YAML for pure registries / structured records; YAML frontmatter for high-frequency structured fields embedded in prose. Don't pick format because "AI reads it" or "humans read it" — both can handle either.

2. **State and Spec must be separated.** One is high-frequency (state changes constantly); the other is low-frequency (spec rarely changes). Mixing them causes high-frequency edits to pollute stable spec content.

3. **Events are append-only.** The audit trail is a feature, not pollution. Reversals are recorded as new events with explicit `prior_decision_id` references, not by editing past entries.

4. **Responsibility split ≠ file split.** AI and human read the same set of files at different cadences. AI continuously updates state; human reviews at three boundaries (session start, task completion, decision point). The architecture does not need separate "AI files" and "human files."

5. **Boundaries enforced by mechanism, not discipline.** Where possible, use mechanisms (e.g., `.aiignore` files for read-only directories) rather than relying on humans or AI to remember rules.

6. **No preventive over-engineering.** Do not add structure for problems that have not occurred. Audit scripts, canonical number ledgers, state consistency checkers — all useful, all should be added **when a real signal demands them**, not preemptively.

7. **Simple enough to maintain without discipline.** If the architecture requires constant discipline to follow, it will fail after a few reversals. The architecture must be simple enough that the path of least resistance is also the correct path.

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

**Convergence test**: if a single event requires editing more than 3 files, the architecture is being violated somewhere.

---

## 7. STATE.md anatomy

This is the file that does most of the heavy lifting. Detailed structure:

### 7.1 YAML frontmatter (current state — overwrites on update)

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

This block is **overwritten** on every update. It represents the current snapshot.

### 7.2 Task graph (semi-stable)

A table showing each task's status (`done` / `in_flight` / `blocked-by`) and a one-line description. Updated when tasks transition states.

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

## 8. Comparison with traditional doc-driven workflow

| Traditional | This architecture |
|---|---|
| Documents are streams (changes leave prose trails) | Documents are snapshots (fields overwrite, events append) |
| Status information scattered across prose | Status is field-structured; prose only carries narrative |
| Reversal = sync prose in multiple files | Reversal = one new event + ≤3 field updates |
| History lives in prose paragraphs (unsearchable) | History lives in git log + event log (greppable) |
| Humans and AI read different prose summaries | Humans and AI read the same files at different cadences |
| Complexity grows unbounded | Complexity grows linearly with real demand |

---

## 9. Final philosophy

> **Each event is written once; one writing lands in one location; one location carries one content type.**

Every rule in this specification derives from these three clauses. If you ever need to extend the architecture, derive your extension from these clauses — do not add new principles.

---

**Implementation guidance**: see [HOWTO.md](HOWTO.md) for operational details, [PATTERNS.md](PATTERNS.md) for known failure modes and anti-bloat patterns, and [case-studies/](case-studies/) for two real-world projects (one failure, one success) demonstrating the architecture.
