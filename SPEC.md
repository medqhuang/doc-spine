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
| 4 | **Strategy + stages + red lines** (the project's founding frame: what it is, the bet, the forks, what must not be violated) | Near-zero (written at bootstrap; amended only by an explicit decision) | Human-primary; AI auto-respects | `docs/ROADMAP.md` | MD |
| 5 | **Cross-project knowledge** (tooling quirks, compute env gotchas) | Low | AI across sessions | `~/.claude/projects/.../memory/` or equivalent persistent memory | MD |

There is also an implicit **type 6: pure registry** (resource inventories, asset paths) — use YAML when it appears, but in a typical research project this is rare enough not to require a default location.

**The absence of a 6th type should be the default state.** Adding new types only when real demand emerges (e.g., a paper-writing phase that needs a canonical numbers ledger) is the architecture working as intended.

Note: a pivot-heavy phase generates a lot of **intermediate scratch** (exploration drafts that are not yet any of the five types). That is *not* a 6th content type — it is process material whose canonical source is undecided. It gets a holding area + catalog, not a content-type slot. See §8.4.

### The tutorial (optional)

The five types keep the **project** healthy, not the **owner**. In heavily AI-driven projects they are execution artifacts — event lines, task reports, number ledgers (the analogue of commit history + lab notebook + QC tables). The reasoning that produced them lives in the agent's working context and never lands on disk. Observed failure at handover (audited across 7 real projects): the owner cannot restore mastery from the spine, because reading it means reconstructing the domain story from engineering logs.

Projects that need a human to (re)take ownership of the subject matter opt into one further content type, written **once at project closeout**, then kept in sync:

| Content type | Answers | Canonical location | Charter (contract) |
|---|---|---|---|
| **Tutorial** | "what is this project" — teaches the domain story to a zero-context reader, image-first | `docs/tutorial/` | [`templates/TUTORIAL_WRITING_STANDARD.md`](templates/TUTORIAL_WRITING_STANDARD.md) (zh) |

Architectural fit — two clauses keep it entropy-safe:

1. **A downstream view that owns the narrative, never the facts.** The tutorial produces no number; every figure/value is a one-hop pointer into the ledger/reports. Single Source holds: facts stay owned upstream, the tutorial owns only their prose reconstruction (which no upstream file owns).
2. **Claim-affecting reversals extend the §6 update set.** A `decision_reversal` that touches a claim must sync the tutorial **in the same pass** — the charter makes this obligation explicit. Projects with a tutorial accept a larger (still bounded, still explicit) locations-per-event count for this one event class.

> *This subsection registers one optional content type for the handover need (evidence: a 7-project audit, 2026-07). Its full regulation lives in the charter file; do not extend it here. The charter is currently Chinese-language — translations welcome.*

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
├── CLAUDE.md ⭐               <40 lines — 5-content-types table + Hard rules + Commit policy
├── README.md                  ≤30 lines — top-level layout + entry table
├── HOWTO.md                   (optional) — local quick-reference for the architecture
├── docs/
│   ├── ROADMAP.md             strategy / stages + forks / red lines — founding frame, near write-once
│   ├── CONVENTIONS.md         naming / git workflow / frozen boundaries
│   └── _archive/              historical snapshots (move during reorg, do not delete)
├── instances/
│   ├── <frozen_baseline>/     .aiignore enforces read-only
│   └── <active>/
│       ├── STATE.md ⭐⭐      YAML frontmatter (current state) + Task graph + Event log + Sub-instance roster
│       ├── README.md          instance entry + stage directories + historical doc topic navigation
│       ├── docs/TASKS.md      T1-TN spec (pure spec, no status)
│       ├── reports/T<N>_report.md   completion reports + data trace
│       ├── scratch/           (pivot-phase only) non-canonical exploration drafts + INDEX.md catalog; must promote — see §8.4
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
     ├──→ ROADMAP.md (only when the frame is amended; rare)     │
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

The fields below are **optional** — add them only when the truth doesn't already live elsewhere. **Omit `in_flight_jobs` / `next_action` / `last_updated` if the truth lives in an external system (SLURM queue, CI status, git mtime) or in the event log itself**; field-izing them duplicates state that will go stale and force reconciliation cost. Likewise **`stage` is not a field** — the active stage is the ROADMAP stage containing `active_task` (already visible in `active_subinstance`'s `<stage_NN>_` path prefix), so field-izing it would duplicate that and drift; see ROADMAP §stages for the stage↔task relationship.

```yaml
---
active_task: <task_id>
active_subinstance: <relative_path>
in_flight_jobs:
  - {jid: <id>, type: <description>, depends_on: <id_or_null>}
next_action: "<concrete next step description>"
last_updated: <YYYY-MM-DD>
---
```

This block is **overwritten** on every update; it represents the current snapshot.

**Example — a load-bearing singular claim.** When the project has one high-frequency, load-bearing assertion (a paper's thesis, a chosen working hypothesis), give it a dedicated field rather than letting it sprawl into `active_task` prose or into scratch files:

```yaml
thesis: "<one-line current claim> · confidence: <optional qualifier, e.g. tentative/supported> · support: <pointers to reports> · lineage: A →[date]→ B (full history in event log)"
```

Overwrite it on each pivot; append the reversal to the event log. This keeps `active_task` a pure focus pointer and keeps the thesis readable in exactly one place. See §8.4.

### 7.2 Task graph (semi-stable)

A table showing each task's status (`done` / `in_flight` / `blocked-by`) and a one-line description. **Overwritten** when tasks transition states — this is a derived view of the event log, kept inline for fast scanning. The `blocked-by` column *is* the task dependency graph; there is no separate DAG file.

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
- `decision` — a choice locked (which fork of ROADMAP §stages was taken, which method over which); the standing set of locked decisions is these events minus their reversals — one grep, no separate table (§7.1: do not field-ize what the log already holds)
- `decision_reversal` — prior decision overturned
- `structural` — reorganization / file move

**Claims, not facts.** A claim-bearing event (`discovery` / `milestone` / `decision` / `decision_reversal`) records the current best-supported understanding, not an absolute truth: the summary carries the key data **with its uncertainty**; the consequence notes what remains open or what observation would overturn the conclusion. Still one line — what we believe · why · what would change it.

**The summary segment carries a subject.** In a claim-bearing event, the segment before the first ` — ` is one plain-language clause with an **explicit subject** — which quantity, which decision, which gate; what changed; what it now means — readable by someone who was not in the session that wrote it. Handles (gate names, task ids, coined abbreviations), values, and refs follow after the dash.

```
- YYYY-MM-DD · task:T<N> · milestone · <plain clause, subject explicit> — <handles · value ± uncertainty · JID/refs · what remains open>
```

This is a constraint on the existing summary segment, not a new field: same one line, same single location, append-only intact.

**Why a subject and not a length cap.** A length cap buys nothing — the writer meets it by compressing harder. The subject is what compression drops *first*: it is the token most recoverable from live context and least recoverable from the file. A subject-less summary reads correctly only against the context that produced it; the next write resolves the referent against a different context and attaches the claim to the wrong object. Observed (2026-07, one project): two distinct quantities were compressed into the same subject-less phrase, mis-attached by a write one day later, and recovered only by a dedicated correction event. **The reader this protects is the next writer, not the human** — a human can interrogate the running session; a cold write cannot.

Non-claim-bearing events (`action`, `structural`, and their kin) are exempt. They record what was done, where a mis-resolved referent costs a re-read rather than a wrong claim.

Where a claim lives — one role per home:

| Home | Owns |
|---|---|
| `thesis` field (§7.1, §8.4) | the currently adopted understanding (the one load-bearing claim) |
| event log line | the change that moved it — evidence + uncertainty, at the moment it happened |
| report claims ledger (templates/CONVENTIONS §6) | the claim summary frozen at task completion |

> *Anything richer per-claim inside STATE (status grades, evidence lists, falsification registries) stays prohibited: evidence is canonical in `reports/` — a claims ledger inside STATE would be a second evidence home, §8.1's drift in a new coat.*

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

### 8.4 Pivot-phase scratch sprawl and the load-bearing claim fragmenting

**Symptom**: During an exploratory phase with frequent thesis / strategy pivots, intermediate documents multiply at the edge of the five types (derivation drafts, competitor audits, reframe proposals, probe plans). They pile into `instances/<active>/docs/` next to `TASKS.md`. Worse, the project's one load-bearing claim — the paper's thesis — ends up asserted across several of them, each labelled "authority." The STATE frontmatter then swells with prose reconciling which copy is current ("authority = A + B; do not trust stale C").

**Root cause — two compounding errors**:
1. These artifacts have no home. None of the five content types fits them (not task-spec, not final report, not cross-phase strategy), so they accrete into the spec directory and get silently read as canonical.
2. A scratch file is allowed to be a single source. But "the current thesis" is type-1 state (overwrite) and/or type-4 strategy (a locked decision) — never a property of a draft file. When a draft holds the authority, every pivot forks a new file and the frontmatter degrades into prose reconciliation. This is §8.1's prose-drift, recurring one level down.

**Fix — a holding area, a promotion rule, and a dedicated field**:
- **Give scratch its own directory, separate from the spec.** Intermediate exploration docs live in `instances/<active>/scratch/` — a sibling of `docs/` and `reports/`, never mixed in with `TASKS.md`. The directory name itself signals "not canonical." A catalog file `scratch/INDEX.md` lists each doc with its status (active / superseded) and its **promotes-to** target; status lives in the INDEX, never in the doc body (per §8.2).
- **Scratch is non-canonical and must promote.** Any content that becomes authoritative is **promoted** to its real single source (STATE / ROADMAP / report), leaving the scratch doc as a pointer. A scratch doc still holding an unpromoted authority is the drift signal — not the file count (see below).
- **A load-bearing singular claim gets a dedicated overwrite field.** The current thesis is one high-frequency overwrite value → a named frontmatter field in STATE.md (§7.1); pivots overwrite it in place, reversals append to the event log. It is read in exactly one place; the file fork disappears.

**On sprawl — volume is not the failure; a retained authority is.** A scratch directory will grow, and that is fine: by construction it holds no canonical content and no status prose, so it cannot produce the version-drift of §8.1 no matter how large — it is a safely-ignorable region. The health metric is not the directory's size but whether its **active** set contains any doc cited as an authority while still sitting in scratch. Do not schedule periodic cleanup (that is maintenance bloat, §9.3). Instead, at natural boundaries — task completion (HOWTO Scenario B), stage end — mark any draft already absorbed by a report as superseded in the INDEX; the file stays in place for its audit links, moving only from the "active" list to the "superseded" list. The active list — the only part read day-to-day — stays small.

> *Why this is not a 6th content type (cf. §3): a content type is "content with a canonical single source." Intermediate scratch is by definition content whose single source is not yet decided — promoting it IS deciding. Minting it as a content type would license exactly the SOT-squatting this fix removes.*

### 8.5 The strategy file goes stale while the project runs

**Symptom**: `docs/ROADMAP.md` last touched at bootstrap. Two months later the project has added tasks, crossed a fork, locked a target venue — none of it in the file. The decisions surface instead in STATE's `thesis` field (which swells from one line to several thousand characters) and in the event log; the task dependency graph lives only in the task graph's `blocked-by` column. Observed in 3 of 4 active projects (2026-08); nobody noticed, because nothing read the file.

**Root cause**: one file, two update frequencies. Its founding half (what the project is, the bet, stages, red lines) is written once and is still correct months later. Its running half (task DAG, locked decisions) must be written at exactly the moments the hand is already in STATE — a task created, a decision taken. Writing a second file at that moment is a separate act of discipline, and §4.4 says discipline loses. The one project whose ROADMAP stayed current was still pivoting weekly; execution-phase projects never touched it again.

**Fix — split by update frequency, not by topic.** Everything that accumulates is already in STATE and stays there: dependencies = the task graph's `blocked-by` (§7.2); locked decisions = the log's `decision` events minus their reversals (§7.3) — no second table; if replaying them ever proves too costly, that is the failure that earns one (§9.3). ROADMAP keeps only the founding frame, amended by explicit decision — a file that holds only what does not change cannot go stale.

> *Predictor, not a rule (§9.3): a file stays current when writing it is a step on the path of doing the work (`TASKS`: no spec, no task; `scratch/INDEX`: no row, no draft; reports: no report, no completion). It decays when writing it is a separate obligation. Test a proposed content type against this before giving it a file.*

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

## 10. Drift detection

Split by **what a check examines**, which decides when it can run. A check with no trigger is a check nobody runs.

### At the commit boundary — this session's diff (HOWTO Scenario D)

These cost nothing extra: the diff is already in front of whoever is committing.

- **Locations-per-event** — count the files this one event changed. Healthy ≤3; at ≥4, name which of them is a second home for something, and fix that before committing.
- **Status prose leak** — grep the *diff*, not the tree, for `in-progress` / `pending` / `SUPERSEDED` / `当前状态` / `YYYY-MM-DD update` being **added** outside `STATE.md` / `STATE.yaml`. Cheapest to remove before it lands.
- **Frontmatter drift** — only if this diff touched STATE frontmatter: do the new values agree with the task graph and the newest event? Frontmatter says `active_task: T2` while the body still narrates T1 = silent drift.
- **Subject-less claim** — for each claim-bearing event (`discovery` / `milestone` / `decision` / `decision_reversal`) this diff appends, read only the segment before its first ` — `, out of context. If it does not name what the claim is about, rewrite before committing: the next writer will resolve the referent against a context that no longer exists (§7.3).

### On demand — accumulated state, not this change

No cadence prescribed (a scheduled sweep is maintenance bloat, §9.3). Run when something reads wrong, or when picking a project back up after a gap.

- **Inter-section redundancy in entry files** — read `CLAUDE.md` / `README.md` top to bottom; ask "does §B say anything §A didn't?" If §B is a reframing of §A, merge or remove.
- **Scratch retaining an authority** — if the project has a `scratch/` area (§8.4), scan its INDEX `active` list: any draft cited as an authority by STATE / ROADMAP / a report, or with an empty `promotes-to`, is an unpromoted authority — promote it and leave a pointer. Directory size itself is *not* a drift signal; a retained authority is.
- **ROADMAP edited by a task** — `git log -- docs/ROADMAP.md`: a commit that touched it without a `decision` event in the same commit is running content that belongs in STATE (§8.5). If this keeps happening, the file's name is pulling it back; rename then, not before.
- **Stale tutorial** — if the project has a tutorial (§3), compare its `last_synced` against the newest claim-affecting event in `STATE.md`. Older = stale-by-definition; a reversal absorbed by the spine but not by the tutorial is exactly the drift this layer must never carry.

---

## 11. Final philosophy

> **Each event is written once; one writing lands in one location; one location carries one content type.**

Every rule in this specification derives from these three clauses. If you ever need to extend the architecture, derive your extension from these clauses — do not add new principles.

---

**Implementation guidance**: see [HOWTO.md](HOWTO.md) for day-to-day operations, and [case-studies/](case-studies/) for two real-world projects (one failure, one success) demonstrating the architecture.
