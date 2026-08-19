# <PROJECT_NAME> · ROADMAP

> Project strategy — the founding frame: what this project is, the bet it makes, its stages and forks, its red lines, and what it knowingly defers. Written at bootstrap; amended only by an explicit decision (append a `decision` event to STATE, then edit this file in place). Read at decision points, not every session.
> What **accumulates** while the project runs — task dependencies, the decision taken at each fork — lives in `instances/<ACTIVE_INSTANCE>/STATE.md` (task graph `blocked-by` column; `decision` events), never here. Instance-level task detail → `instances/<ACTIVE_INSTANCE>/docs/TASKS.md`.

---

## Project · what and why

<2-4 sentences: what does this project do? What's the goal? What's the deliverable?>

Main object chain (if applicable):

```
<upstream input> → <processing step> → <downstream output>
```

Central hypothesis / method bet (if the project has one): <the structural claim this project exists to test — its *shape*, not its current status; the live claim with confidence and lineage is STATE's `thesis` field>

---

## Strategy / stages

> A **stage** is the coarse, decision-driven grouping of one or more **tasks** (`TASKS.md` = the fine, execution-driven unit). **You only ever advance tasks** (HOWTO Scenario B); the active stage is simply the one containing `active_task`. You cross into the next stage when its entry conditions (below) hold — recorded as a `milestone` event in STATE, never a separately-tracked status field. Which branch a fork took is likewise a STATE `decision` event; this file only **defines** the fork.

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

## Red lines

Conditions that, if violated, force re-evaluation:

- **<red line 1>**: <consequence if breached>
- **<red line 2>**: <consequence>
- ...

---

## Open problems (acknowledged at bootstrap, deferred)

| Problem | Handling |
|---|---|
| <problem 1> | <how it will be handled; which task addresses it> |
| <problem 2> | <handling> |

> *A problem deferred later, mid-project, is a decision: a STATE `decision` event. This table is not extended.*

---

> *Nothing in this file changes because a task advanced. If you are editing it for any reason other than amending the frame itself (a red line, a stage boundary, the central bet), the content belongs in STATE — see SPEC §8.5.*
