# <INSTANCE_NAME> · TASKS

Task spec only. Live status and history → [`../STATE.md`](../STATE.md).

Every task states **what it consumes** and **what counts as done or when to stop**; the other fields are used when they apply. A computation is specified by its steps and criteria; an exploration by its question and stopping rule — forcing steps onto a search only produces fake steps.

## Task graph (static)

| Task | One-line spec | Depends on |
|---|---|---|
| T1 | <task description> | — |
| T2 | <task description> | T1 |
| T3 | <task description> | T2 |

---

## Task details

### T1 · <a computation-shaped task>

**Input**: <what this task consumes — upstream artifacts, parameters, data>

**Steps**:
1. <step 1>
2. <step 2>

**Output**: <what this task produces — files, data, deliverables>

**Completion criteria**:
- <measurable criterion — a scientific quantity passes with value **and** reliability (uncertainty / convergence / validity range), not the value alone>

**Known risks**:
- <risk>: <mitigation>

**Locked decisions** (rationale → STATE `decision` events; project-level constraints → ROADMAP §red lines):
- <decision>: <one-line summary + reference>

---

### T2 · <an exploration-shaped task>

**Question**: <the one question this task exists to answer>

**Why it matters**: <what changes downstream depending on the answer>

**Input**: <what it starts from — data, candidates, prior results>

**What would settle it**: <the observation / calculation / argument that decides the question either way>

**Stopping rule**: <budget or condition — N candidates screened, M core-hours, a date, "first candidate that passes the gate". An exploration without one never closes>

**Constraints**: <red lines and standing decisions that bound the search>

**Output**: `reports/T2_report.md` — written even when the answer is "none survived": what was tried and why each was rejected is the deliverable.

---

## Known sub-instances (spec-level registry)

| Path | Role |
|---|---|
| <path> | <role> |
