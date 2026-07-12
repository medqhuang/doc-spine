# <INSTANCE_NAME> · TASKS

Task spec only. **Current state / in-flight jobs / event log → [`../STATE.md`](../STATE.md)** (single source).

## Agent read path (this file = L2)

L0-L4 layering: see [`../../../docs/ROADMAP.md`](../../../docs/ROADMAP.md) §Agent read path. This file carries **task spec only** (input / steps / output / completion criteria / known risks / locked decisions).

## Task graph

> **Live status (✅/🔄/⏳ + JID + progress) → [STATE.md](../STATE.md)**. The table below is the static task list / dependency graph.

| Task | One-line spec | Depends on |
|---|---|---|
| T1 | <task description> | — |
| T2 | <task description> | T1 |
| T3 | <task description> | T2 |
| ... | ... | ... |

---

## Task details

### T1 · <Task title>

- **Required reading**: this section + ROADMAP §<relevant sections>
- **Optional reading**: <pointers to relevant documents / sub-instances>
- **Skip**: <pointers to unrelated sections>

**Input**: <what this task consumes — upstream artifacts, parameters, data>

**Steps**:
1. <step 1>
2. <step 2>
3. <step 3>

**Output**: <what this task produces — files, data, deliverables>

**Completion criteria**:
- <measurable criterion 1 — a scientific quantity passes with value **and** reliability (uncertainty / convergence / validity range), not the value alone>
- <measurable criterion 2>

**Known risks**:
- <risk 1>: <mitigation>
- <risk 2>: <mitigation>

**Locked decisions** (rationale references — see ROADMAP §locked decisions or STATE event log):
- <decision 1>: <one-line summary + reference>
- <decision 2>: ...

**Known pitfalls → [memory](~/.claude/projects/<PROJECT_PATH>/memory/)** (cross-project knowledge). **In-progress status / in-flight JIDs / history** → [STATE.md](../STATE.md).

---

### T2 · <Task title>

(same structure as T1)

---

## Known sub-instances (spec-level registry)

> **Status (active/done/deprecated) + related JIDs + history → [STATE.md](../STATE.md)** §Sub-instance roster + event log. This table records only `path → role` (spec-stable).

| Path | Role |
|---|---|
| <path> | <role> |

---

**Versioning note**: this file is the pure-spec partner of STATE.md. Spec changes (e.g., new task added, completion criterion refined) are committed as discrete events; spec drift (TBDs that became defined) just gets overwritten in place.
