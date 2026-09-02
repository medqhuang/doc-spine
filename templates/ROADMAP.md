# <PROJECT_NAME> · ROADMAP

> The founding frame: what this project is, the bet it makes, its stages and forks, its red lines, what it knowingly defers. Written at bootstrap; amended only by an explicit decision (a `decision` event in STATE, then edit here in place). Read at decision points, not every session.
> A **stage** groups tasks; you only ever advance tasks (`TASKS.md`), and the active stage is the one containing `active_task`. What accumulates while the project runs — task dependencies, the branch a fork took, problems deferred mid-project — lives in `instances/<ACTIVE_INSTANCE>/STATE.md` (task graph, `decision` and `question` events), never here (SPEC §8.5).

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

**Stage entry conditions** (all must hold for the primary path):
1. <condition 1>
2. <condition 2>

If any fails → Stage 3b. Crossing into a stage is a `milestone` event in STATE; which branch a fork took is a `decision` event.

---

## Red lines

Conditions that, if violated, force re-evaluation:

- **<red line 1>**: <consequence if breached>
- **<red line 2>**: <consequence>

---

## Open problems (acknowledged at bootstrap, deferred)

| Problem | Handling |
|---|---|
| <problem 1> | <how it will be handled; which task addresses it> |
| <problem 2> | <handling> |
