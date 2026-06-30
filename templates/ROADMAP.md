# <PROJECT_NAME> · ROADMAP

> Project-level strategy + stages + locked decisions + red lines + DAG. Cross-instance methodological commitments live here.
> Instance-level task detail → `instances/<ACTIVE_INSTANCE>/docs/TASKS.md`. Live state → `instances/<ACTIVE_INSTANCE>/STATE.md`.

## Agent read path (progressive disclosure)

| Layer | Document | When to read |
|---|---|---|
| **L0** | System-injected (project root `CLAUDE.md` + global user instructions) | Auto-loaded |
| **L1a** | `instances/<ACTIVE_INSTANCE>/STATE.md` (state + event log) | Every session start |
| **L1b** | This file (project strategy + red lines + DAG) | Each task start |
| **L2** | Instance `docs/TASKS.md` §relevant task | Current task start |
| **L3** | Instance `reports/T<N>_report.md` / sub-instance inputs/sbatch | When task references it |
| **L4** | `docs/CONVENTIONS.md` / registries / `_legacy/` / `_archive/` | Rarely; on-demand only |

**Throttling**: do not read L4. Do not read sub-instance docs that the current task does not hit. **STATE.md is the single status authority** — never look for current status in TASKS / reports.

---

## Project · what and why

<2-4 sentences: what does this project do? What's the goal? What's the deliverable?>

Main object chain (if applicable):

```
<upstream input> → <processing step> → <downstream output>
```

---

## Strategy / stages

> A **stage** is the coarse, decision-driven grouping of one or more **tasks** (`TASKS.md` = the fine, execution-driven unit). **You only ever advance tasks** (HOWTO Scenario B); the active stage is simply the one containing `active_task`. You cross into the next stage when its entry conditions (below) hold — recorded as a `milestone` event in STATE, never a separately-tracked status field.

```
Stage 1   : <description>
            Deliverable: <what>
   ↓
Stage 1.5 : <conditional / placeholder, if applicable>
   ↓
Stage 2   : <description>
   ↓ decision point
Stage 3a  : <primary path>
   or
Stage 3b  : <fallback path>
```

**Stage entry conditions** (all must hold for primary path):
1. <condition 1>
2. <condition 2>
...

If any fails → fall back to Stage 3b.

---

## Locked decisions

> Decisions that are not up for reconsideration unless red-lined. Reversals append to STATE event log; this file records the current locked state.

### <Decision category 1>

- **Choice**: <what was chosen>
- **Date**: <when locked>
- **Rationale**: <why; can be terse, with pointer to source>

### <Decision category 2>

(same structure)

---

## Open problems (acknowledged, deferred)

| Problem | Handling |
|---|---|
| <problem 1> | <how it will be handled; which task addresses it> |
| <problem 2> | <handling> |

---

## Red lines

Conditions that, if violated, force re-evaluation:

- **<red line 1>**: <consequence if breached>
- **<red line 2>**: <consequence>
- ...

---

## Data / dependency DAG (if applicable)

```
<sketch of upstream → downstream dependencies>
```

---

## Project history

Milestones / decision reversals / state changes → [`instances/<ACTIVE_INSTANCE>/STATE.md`](../instances/<ACTIVE_INSTANCE>/STATE.md) §event log (append-only) + `git log`.

---

## Entry table

| Looking for | Go to |
|---|---|
| **Live state / active task / in-flight jobs** | [`instances/<ACTIVE_INSTANCE>/STATE.md`](../instances/<ACTIVE_INSTANCE>/STATE.md) ★ |
| Task spec | [`instances/<ACTIVE_INSTANCE>/docs/TASKS.md`](../instances/<ACTIVE_INSTANCE>/docs/TASKS.md) |
| Naming / git workflow / 5 content types map | [`CONVENTIONS.md`](CONVENTIONS.md) |
| Active instance overview + historical doc topic nav | [`../instances/<ACTIVE_INSTANCE>/README.md`](../instances/<ACTIVE_INSTANCE>/README.md) |
