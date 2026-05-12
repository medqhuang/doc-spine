# Case Study 1 · Failure: documentation entropy in a multi-month research project

> **Anonymized**. Names and specifics have been generalized; the patterns and metrics are real.

## Context

**Project A**: a compute-heavy research project (DFT-class first-principles calculations) with an end goal of submitting a paper to a high-impact journal. Single researcher, working with an AI coding agent over multiple sessions. Roughly **14 days of intense work**, **175 git commits**, **150 markdown files** accumulated.

## How it started

The researcher began with reasonable instincts: document every task, every decision, every reversal. Standard practice. The project used:

- A top-level `CLAUDE.md` (project-wide AI instructions)
- A `docs/ROADMAP.md` (strategy)
- A `docs/TASKS.md` (task list with detailed status per task)
- Per-task completion reports in `analysis/task<N>/REPORT.md`
- A `logs/decisions.md` (event log, freeform prose)
- A top-level `MD_INDEX.md` listing every markdown file in the project

By day 7, this seemed well-organized. By day 14, it was unreadable.

## The failure mode in detail

### Pattern 1 · Status accumulation in TASKS.md

The `TASKS.md` §"current status" section started as a 5-line summary. Each significant event (reversal, new task launch, milestone) added a new paragraph. By day 14, it contained **four stacked update paragraphs**, each 8-12 lines, dated, mixing prose narrative with key data points. Reading the file required understanding which paragraph superseded which.

Quote from a real entry:
> "2026-05-04 update — Task 2 phonon FAIL, geometry shortcut overruled, NC vc-relax upgraded to mainline: Task 2 NCPP chain after resubmission 31/31 COMPLETED (JOB DONE, ExitCode 0, mechanical flow OK), but **phonon physical criterion FAIL** — direct dynmat.x asr=no on dyn1..dyn32 revealed Z-point in-plane TA doubly degenerate ω = -53.7 cm⁻¹ ..."

This is the same content that, in a healthier architecture, would have been a single-line event log entry: `2026-05-04 · task:T2 discovery · phonon FAIL: Z-point TA -53.7 cm⁻¹; root cause: 0.1% c-strain × layered vdW Grüneisen γ~5-10; action: new vc-relax sub-instance, see <event ref>`.

### Pattern 2 · Stacked SUPERSEDED banners in reports

Per-task reports developed multi-layer SUPERSEDED banners at the top:

> ⚠️ SUPERSEDED 2026-05-02 → SUPERSEDED + new path landed 2026-05-11
> §1-9 records ... but is now downgraded to cross-check ...
> §10 (2026-05-03) reflects ...
> §11 (2026-05-11) reflects ...

Each banner was logically necessary at the moment it was added — they truly did supersede prior conclusions. But stacking them in prose form (instead of recording them as events in a single log) made the report headers grow unboundedly. A reader entering cold could not tell what was current without parsing the whole banner stack.

### Pattern 3 · The same decision recorded in 6 places

When a major reversal occurred (changing the computational method), the researcher dutifully recorded the reasoning in:

1. `docs/ROADMAP.md §locked decisions`
2. `docs/TASKS.md §current status` (new paragraph)
3. The affected report's SUPERSEDED banner
4. The old sub-instance's `CLAUDE.md` (added a DEPRECATED banner)
5. The new sub-instance's `CLAUDE.md` (added a SCAFFOLD banner)
6. `logs/decisions.md`

Six prose updates for one event. Each was a slightly different framing of the same decision. None was clearly canonical. **Drift was guaranteed**: by week 2, three of them had subtly different versions of the rationale.

### Pattern 4 · The CLAUDE.md routing manual itself becomes a symptom

The project's `CLAUDE.md` grew to 142 lines with elaborate routing tables:
- "If you want X, look at file Y"
- "If files Y and Z conflict, Y wins unless ..."
- "Edit permissions: file W is human-only; file X needs approval"

The routing table existed precisely because there were too many files saying overlapping things — a manual was needed to navigate them. The manual itself became another file requiring maintenance, with its own update prose.

## The terminal observation

The researcher made the breakthrough observation after re-reading the manuscript draft, which had been heavily AI-co-authored:

> "I cannot verify the truthfulness of this manuscript anymore. The AI wrote prose grounded in the documentation. The documentation has drifted. I cannot tell which numbers are current. I cannot trust my own files."

This is the **endpoint of unchecked documentation entropy**: not "we have too many files," but **"the project itself is no longer something I own intellectually."**

## Metrics at the breakdown point

| Metric | Value |
|---|---|
| Total markdown files | 150 |
| Total markdown lines | ~3 MB / ~75,000 lines |
| Sections labeled "session entry doc" | 5 (each from a different phase) |
| Places where the latest major decision was recorded as prose | 6 |
| `logs/decisions.md` length | 2,200 lines |
| Single-decision audit chains traversing more than 3 files | ~12 |
| Time to verify a single number's provenance | 5-15 minutes (had to read multiple file versions) |

## What was tried (before this architecture existed)

The researcher attempted several conventional fixes:
- **Adding more discipline**: writing more careful update paragraphs. Did not help; the underlying multiplication continued.
- **Adding more routing**: expanding `CLAUDE.md` routing rules. Made the routing manual itself a maintenance burden.
- **Auditing periodically**: spot-checking which files were "current." Worked locally but did not prevent re-occurrence.

None of these address the root cause: **prose is not a state container**. State that lives in prose drifts. The only fix is to remove state from prose entirely.

## What worked (in the recovery / next project)

See [case-study 2](02-success-state-sourced.md). The same researcher started a second related project with **state explicitly extracted into a single source** (`STATE.md`) and **prose carrying only narrative**. The second project did not exhibit any of the failure patterns above through multiple decision reversals and ~3 weeks of active work.

The architecture documented in [SPEC.md](../SPEC.md) is the crystallization of that recovery.

## Lessons (in priority order)

1. **Status updates accumulating in prose is the root failure**, not "too many files." Even 10 well-organized files can drift if status lives in prose.
2. **No amount of discipline saves you** once accumulation begins. The path of least resistance — adding "just one more update paragraph" — is irresistible to both human and AI.
3. **The routing manual is a downstream symptom**, not a solution. If your CLAUDE.md needs to be 100+ lines to navigate your docs, you have too many overlapping files.
4. **You stop trusting your own work**. This is the worst symptom — much worse than wasted tokens. Once trust breaks, the option of "just submit the paper" disappears, because you can't verify what you'd be signing.

## Anonymization notes

This case is based on a real research project. Identifying details have been generalized: the physical system, the specific computational method, the journal, and the researcher's identity. Quantitative metrics (file counts, commit counts, banner stacks) are accurate; specific numbers from the science are abstracted.
