# Case Study 2 · Success: state-sourced architecture in a follow-up project

> **Anonymized**. Same researcher as [case-study 1](01-failure-doc-bloat.md), second project, lessons applied from the failure.

## Context

**Project B**: a follow-up compute-heavy research project, conceptually adjacent to Project A but with new methodology. Same researcher, same AI collaboration mode, same multi-month timeline. Started **after the Project A failure was diagnosed** but before any architecture was formally specified.

The researcher began with one explicit principle inherited from Project A's failure: **status must not live in prose**. Everything else was discovered during execution.

## How it was set up

Directory layout follows [SPEC §5](../SPEC.md). Concrete sizes in Project B: `CLAUDE.md` 24 lines / `README.md` 21 / `HOWTO.md` 102 / `docs/ROADMAP.md` 156 / `docs/CONVENTIONS.md` 93 / `docs/HPC_inventory.yaml` 145 / instance `STATE.md` 66 / instance `README.md` 65 / `docs/TASKS.md` 411 (spec only) / `reports/T1_report.md` 444 (data trace). A frozen baseline instance was carried over with `.aiignore` enforcing read-only.

### The single source: STATE.md

The architecture's load-bearing file. Three sections + YAML frontmatter:

```markdown
---
stage: 1
active_task: T2
active_subinstance: <path>
in_flight_jobs:
  - {jid: 7254803, type: SCF main, ...}
  - ...
next_action: "wait for chunks 31/31; then validate Z-point dynmat..."
last_updated: 2026-05-11
---

# Instance State

## Task graph
| T0 | ✅ ... | T1 | ✅ 2026-05-11 | T2 | 🔄 in-flight | T3 | ⏳ blocked T2 | ...

## Event log (append-only, one line each)
- 2026-04-27 · structural · reorg ...
- 2026-04-28 · decision · stage 1 path locked: <method choice>
- 2026-04-30 · task:T1 milestone · path A landed (control)
- 2026-05-02 · decision_reversal · path A → B (3 reasons: ...)
- 2026-05-03 · task:T1 milestone · sanity cross-check passed under path B
- 2026-05-04 · task:T2 discovery · phonon FAIL (Z-point TA -53.7 cm⁻¹) → new sub-instance
- 2026-05-11 · task:T1 milestone · NC vc-relax production done (jid 7252125, key results: ...)
- 2026-05-11 · task:T2 action · chain resubmitted (double-fix: NC geom + occupations=fixed)
- ... (16 total events)

## Sub-instance roster
| Path | Role | Status |
| <path>/01_v1 | T1 legacy path | closed |
| <path>/02_uspp_obsolete | T2 USPP path | deprecated (per 2026-05-02 reversal) |
| <path>/02_nc_main | T2+T3+T4 NC main chain | production in-flight |
| ...
```

That's the whole status story. **In one file. Single source.**

## What did NOT happen (the absence of failure)

Through the project's 3 weeks of active work, including 2 major decision reversals:

- ❌ No `SUPERSEDED` banners stacked on report headers. The T1 report was written once. Its header has a clean YAML frontmatter (status / paths / final values). No banner accumulation.
- ❌ No `§current status` paragraphs in TASKS.md. TASKS.md is pure spec.
- ❌ No "2026-XX-XX update" paragraphs anywhere. Each event was a single line in STATE.md log.
- ❌ No 6-place sync on the major reversal. The PP-choice reversal touched: (1) STATE event log [+1 line], (2) ROADMAP §locked decisions [+1 line], (3) two affected sub-instances flagged in STATE roster [+2 status updates]. Total: 4 single-line updates in 2 files. Not 6 prose paragraphs in 6 files.
- ❌ No "I don't trust my own docs" moment. Everything is greppable.

## Quantitative comparison

| Metric | Project A (failure) | Project B (success) |
|---|---|---|
| Total markdown files (excluding archive/venv) | 150 | ~30 (active spine) |
| Total markdown lines (active) | ~75,000 | ~1,470 |
| Sections labeled "session entry doc" | 5 | 1 (`CLAUDE.md`) |
| Places where the latest major decision was recorded | 6 prose paragraphs in 6 files | 1 event in 1 log, ≤3 field updates |
| `decisions.md`-equivalent length | 2,200 lines of prose | 66 lines of `STATE.md` (frontmatter + 16-line event log) |
| Time to verify a single number's provenance | 5-15 min | <1 min (`STATE` event has report ref → report has data) |
| Time to orient a new session | 5-10 min | <2 min (read STATE frontmatter + last 3 events) |

## What worked (and why)

### 1 · STATE.md as the single status source

The biggest win. Every status query — "what is the current task?", "what are in-flight jobs?", "when was the last reversal?" — answered by one file. Reading STATE.md frontmatter (≤10 lines) is sufficient for 80% of session-start orientation.

### 2 · Event log discipline (append-only, one line per event)

Each significant event becomes a one-line append. Format:

```
YYYY-MM-DD · scope · event type · summary + key data. ref: <pointer>
```

Constraint enforces compression: a multi-paragraph narrative would not fit, so the writer (human or AI) compresses to essentials and points to the report for details. This is the opposite of the failure mode in Project A, where every event triggered a fresh prose paragraph.

### 3 · TASKS.md as pure spec

Once status was removed from TASKS.md, it became a stable document. Task spec — inputs, steps, expected outputs, completion criteria, known risks, locked decisions — changes infrequently. In Project B, TASKS.md was edited fewer than 5 times across 3 weeks.

### 4 · Frozen instances enforced by mechanism (`.aiignore`)

Project B contained a frozen baseline instance (a previous methodological MVP, completed and locked). Adding a `.aiignore` file in that directory automatically excluded it from AI's default reading scope. **No discipline required**. Compare to Project A, where the equivalent boundary was enforced by a paragraph in CLAUDE.md that AI occasionally forgot.

### 5 · Format follows content (YAML frontmatter, YAML registry, MD prose)

The HPC resource inventory — pure registry data (paths, sizes, roles) — was migrated from a 130-line MD prose file to a 145-line YAML file. AI now queries it programmatically; humans read it as a structured tree. Both improved.

STATE.md's high-frequency status fields became YAML frontmatter (5 lines of structured data) while the event log remained MD (semi-structured prose). The right format for each content type.

## Anti-bloat patterns observed during evolution

Two micro-patterns emerged from near-failures during Project B's evolution — both crystallized into [SPEC.md §9](../SPEC.md): **don't harden enums** (the scope-dictionary close-set trap) and **precedent boundary annotations** (a special-case rule without a fence will be generalized).

The deeper meta-principle from these observations:

> **In an evolving architecture, the most dangerous omission is not a missing rule — it is a missing boundary.**

This insight emerged from observing Project B's CLAUDE.md path: when section count grew from 4 to 5 (with the STATE writing discipline addition), the natural follow-up question was "should we add similar discipline sections for TASKS and reports?" The answer is no — but without an explicit boundary, the answer drifts toward yes.

## Anonymization notes

This case is based on the same real-world project as case-study 1's "second project" reference. Identifying details (physical system, specific method, software, journal target) have been generalized. Quantitative metrics (file counts, line counts, event log length, JID counts) are accurate.
