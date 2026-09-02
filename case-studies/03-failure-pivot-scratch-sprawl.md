# Case Study 3 · Failure→fix: pivot-phase scratch sprawl inside a healthy state-sourced project

> **Anonymized**. Names and specifics generalized; the patterns and metrics are real. This case is notable because the project was **already using doc-spine successfully** — the failure appeared in a blind spot the original five content types did not cover.

## Context

**Project C**: a compute-heavy research project (same workflow family as [case-study 2](02-success-state-sourced.md) — HPC compute paired with local AI-assisted coding), several months in, aimed at a high-impact journal. The architecture had been healthy throughout: `STATE.md` as single source, append-only event log, write-once reports. None of [case-study 1](01-failure-doc-bloat.md)'s symptoms were present — no stacked `SUPERSEDED` banners, no status prose in `TASKS.md`, no 6-place decision sync.

Then the project entered a **paper-framing phase**: choosing the thesis for the first submission. Over a stretch of intense work, the thesis pivoted **twice in four days** as the analysis matured.

## How it started

The pivot phase generated a different *kind* of document than the architecture had a slot for. Not task-specs. Not completion reports. Not cross-phase strategy. Instead: derivation drafts, competitor literature audits, "submission spine" syntheses, reframe proposals, follow-up probe plans. Each was a legitimate, useful working document. Each was written into `instances/<active>/docs/` — the same directory as the one canonical spec file, `TASKS.md`.

By itself this looked harmless. Every one of those documents obeyed the rules: no status prose, no banners. The old drift checks (grep for `SUPERSEDED` / `当前状态`) found nothing. The architecture reported healthy. It was not.

## The failure mode in detail

### Pattern 1 · Scratch accumulates at the edge of the five types

`instances/<active>/docs/` grew from **1 file** (`TASKS.md`) to **14**. The thirteen new ones were all intermediate exploration artifacts. Because they sat next to the canonical spec, they were read as if they were themselves canonical — peers of `TASKS.md`, not drafts.

### Pattern 2 · The load-bearing claim fragments across drafts

The project's single most important assertion — the paper's thesis — ended up declared, independently, in **three** of those working documents: a "submission-spine" synthesis (which literally labelled itself the *authority*), a reframe proposal (the latest pivot), and a novelty audit. Each was internally consistent; none was canonical; all three claimed to be. A reader entering cold could not tell which stated the current thesis.

### Pattern 3 · The frontmatter degrades into a reconciliation engine

With the authority scattered, `STATE.md`'s frontmatter took on the job of reconciling it. The `active_task` field — specified as a *single focus pointer* — swelled into **four prose paragraphs**, one of them an explicit anti-contamination warning ("❌ DO NOT treat the stale magnitude path as current"). `next_action` ballooned in parallel. The single source of state was now spending most of its frontmatter telling the reader which of the scattered copies to believe.

### The crucial observation

The architecture had not collapsed. The event log was still append-only; reports were still write-once; there were zero `SUPERSEDED` banners. Every individual rule from case-study 2 was being followed. The drift had simply **recurred one level down**: the same prose-accumulation failure as case-study 1's `TASKS.md`, now playing out across a layer the five content types never named — the *process* documents. Single-source discipline had been applied across the five types, but **not recursively to the scratch that feeds them**.

## Metrics at the detection point

| Metric | Value |
|---|---|
| Files in `instances/<active>/docs/` (was 1: `TASKS.md`) | 14 |
| Working docs independently asserting the thesis as "authority" | 3 |
| `active_task` field length | 4 prose paragraphs (spec: 1 pointer) |
| Thesis pivots in the window | 2 in 4 days |
| `SUPERSEDED` banners / status-prose leaks (case-study 1 symptoms) | 0 |

The last row is the point: by the *old* diagnostics the project was clean. The failure was real and the old checks were blind to it.

## Root cause

Two compounding errors, both detailed in [SPEC §8.4](../SPEC.md):

1. **Scratch had no home.** None of the five content types fit the intermediate drafts, so they accreted into the spec directory and were silently read as canonical.
2. **A scratch file was allowed to be a single source.** "The current thesis" is type-1 state (overwrite) and/or type-4 strategy (a locked decision) — never a property of a draft file. Once a draft held the authority, each pivot forked a new file and the frontmatter degraded into prose reconciliation.

## What worked (the fix this case catalyzed)

The recovery is [SPEC §8.4](../SPEC.md), three moves:

1. **A separate `scratch/` directory** — sibling of `docs/` and `reports/`, never mixed with `TASKS.md`. The name signals "not canonical." A `scratch/INDEX.md` carries each draft's status and its **promotes-to** target; status lives in the INDEX, never in the body.
2. **Scratch is non-canonical and must promote** — when a draft's content becomes authoritative, it moves to its real single source (STATE / ROADMAP / report) and the draft becomes a pointer.
3. **The thesis gets a dedicated overwrite frontmatter field** — read in one place, overwritten on each pivot, reversals appended to the event log. `active_task` returns to being a single pointer.

On the natural worry — *won't scratch just become the next bloat?* — no, **provided it never holds an authority**. A scratch directory may grow without harm: it carries no canonical content and no status prose, so it cannot drift no matter its size. The health signal is not the file count but whether the *active* set contains a draft being cited as an authority while still in scratch. Cleanup is not scheduled; at task completion / stage end, absorbed drafts are marked superseded in the INDEX and left in place for their audit links.

## Lessons (in priority order)

1. **Single-source must be applied recursively — including to the scratch that feeds the five types.** A project can pass every type-level check and still drift in the layer below it.
2. **A clean drift-grep is not a clean architecture.** Case-study 1's symptoms (banners, status prose) are one face of entropy; a fragmented load-bearing claim across non-canonical drafts is another, and the old greps miss it.
3. **A load-bearing singular claim needs its own overwrite container.** Left homeless, a high-frequency claim like a thesis will colonize whatever is nearest — scratch files, then the frontmatter — and turn the single source into a reconciliation engine.
4. **The fix is a holding area, not a new content type.** Promoting scratch to a first-class type would have licensed exactly the squatting that caused the failure (see SPEC §8.4's closing note).

> *Later observation (2026-09, eight projects): the catalog half of move 1 — `INDEX.md` with status and promotes-to — went unmaintained (124 drafts, 24 rows) while the failure did not recur. What held was the directory boundary and the `thesis` field; the event log's `ref:` pointers turned out to be the catalog. SPEC §8.4 now lists `INDEX.md` as optional wayfinding.*

## Anonymization notes

Based on a real research project from the same workflow family as case-studies 1 and 2. Identifying details (physical system, specific method, the thesis content, journal target) are generalized. Quantitative metrics (file counts, paragraph counts, pivot counts) are accurate; the science is abstracted.
