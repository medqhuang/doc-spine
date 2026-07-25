---
# Current state (high-frequency update target — AI/tools grep frontmatter; humans read it directly too)
active_task: T0
active_subinstance: <relative_path_or_null>
in_flight_jobs: []
# Examples for in_flight_jobs:
#   - {jid: 12345, type: SCF main, cluster: <hpc-name>, submitted: "2026-XX-XXTHH:MM"}
#   - {jid: 12346, type: <next step>, depends_on: 12345}
next_action: "<concrete next step — what is the AI / human supposed to do next?>"
# thesis: "<one-line load-bearing claim> · confidence: <optional> · support: <ptr> · lineage: A →[date]→ B"   # ONLY if the project has one high-frequency load-bearing claim (e.g. a paper thesis); see SPEC §8.4. Omit otherwise.
last_updated: 2026-XX-XX
---

# <INSTANCE_NAME> · State

> **Single source of truth for state + events.** Frontmatter above = current snapshot (overwrite); task graph + event log below: task graph is a state aggregation (overwrite); **only the event log is append-only**. Discipline → [`../../CLAUDE.md`](../../CLAUDE.md); spec → [`docs/TASKS.md`](docs/TASKS.md); reports → [`reports/`](reports/); strategy → [`../../docs/ROADMAP.md`](../../docs/ROADMAP.md).

## Task graph

| Task | Status | One-line description |
|---|---|---|
| T0 | 🔄 in-progress | <e.g., scoping + decisions> |
| T1 | ⏳ blocked by T0 | <e.g., baseline calculation> |
| T2 | ⏳ blocked by T1 | <e.g., follow-up analysis> |
| ... | ... | ... |

Legend: ✅ done · 🔄 in-flight · ⏳ blocked · ⏸ deferred · ❌ cut

---

## Event log (append-only, one line per event)

> Format: `YYYY-MM-DD · <scope> · <event type> · summary + key data (value ± uncertainty; JID/refs) + consequence / what remains open`
> Scope examples: `project` / `task:T<N>` / `subinstance:<path>` / `lit` (extend as needed)
> Event type examples: `milestone` / `action` / `discovery` / `decision_reversal` / `structural` / `settled`
> Literature settlement (CONVENTIONS §10) appends, e.g.: `YYYY-MM-DD · lit · settled · <DOI→item_key>… · resolved_model · backlog:[…]`

- **YYYY-MM-DD** · project · structural · initial bootstrap from doc-spine template

---

## Sub-instance roster

> Detailed configuration lives inside each sub-instance (`inputs/`, `sbatch/`, `STATUS.yaml` if present). This roster records only `path → role → status`.

| Path | Role | Status |
|---|---|---|
| <e.g., `01_<stage>/<run_name>/`> | <e.g., baseline calculation> | <status> |

---

## Edit permissions / discipline reminder

- **State changes / decisions / events**: append-only to event log above + frontmatter overwrite
- **No prose updates to TASKS / reports / sub-instance docs** for state (those files carry spec / data trace / role only)
- **History corrections**: `git revert` + new event, not in-file editing
- **Cross-project knowledge** (HPC quirks, tool gotchas): goes to user-level memory, not this file
