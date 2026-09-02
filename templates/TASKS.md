# <INSTANCE_NAME> · TASKS

Task spec only — input / steps / output / completion criteria / known risks / locked decisions. Live status and history → [`../STATE.md`](../STATE.md).

## Task graph (static)

| Task | One-line spec | Depends on |
|---|---|---|
| T1 | <task description> | — |
| T2 | <task description> | T1 |
| T3 | <task description> | T2 |

---

## Task details

### T1 · <Task title>

**Input**: <what this task consumes — upstream artifacts, parameters, data>

**Steps**:
1. <step 1>
2. <step 2>

**Output**: <what this task produces — files, data, deliverables>

**Completion criteria**:
- <measurable criterion 1 — a scientific quantity passes with value **and** reliability (uncertainty / convergence / validity range), not the value alone>
- <measurable criterion 2>

**Known risks**:
- <risk 1>: <mitigation>

**Locked decisions** (rationale → STATE `decision` events; project-level constraints → ROADMAP §red lines):
- <decision 1>: <one-line summary + reference>

---

### T2 · <Task title>

(same structure as T1)

---

## Known sub-instances (spec-level registry)

| Path | Role |
|---|---|
| <path> | <role> |
