---
# Current snapshot — overwritten in place. Pointers, not prose (SPEC §7.1).
active_task: T0
active_subinstance: <relative_path_or_null>
in_flight_jobs: []
# in_flight_jobs examples:
#   - {jid: 12345, type: SCF main, cluster: <hpc-name>, submitted: "2026-XX-XXTHH:MM"}
#   - {jid: 12346, type: <next step>, depends_on: 12345}
next_action: "<one concrete next step>"
# thesis: "<one-line load-bearing claim> · confidence: <optional> · support: <ptr> · lineage: A →[date]→ B"   # only if the project has one load-bearing claim (e.g. a paper thesis); SPEC §8.4. Omit otherwise.
last_updated: 2026-XX-XX
---

# <INSTANCE_NAME> · State

> Single source of truth for state + events. Frontmatter = current snapshot (overwrite); task graph = derived view (overwrite); **only the event log is append-only**.

## Task graph

| Task | Status | One-line description |
|---|---|---|
| T0 | 🔄 in-progress | <e.g., scoping + decisions> |
| T1 | ⏳ blocked by T0 | <e.g., baseline calculation> |
| T2 | ⏳ blocked by T1 | <e.g., follow-up analysis> |

Legend: ✅ done · 🔄 in-flight · ⏳ blocked · ⏸ deferred · ❌ cut

---

## Event log (append-only, one line per event)

> Format: `YYYY-MM-DD · <scope> · <event type> · <summary> — key data (value ± uncertainty; JID/refs) + consequence / what remains open`
> Claim-bearing events (`discovery` / `milestone` / `decision` / `decision_reversal`): the summary before the first ` — ` is one plain-language clause with an explicit subject, readable cold (SPEC §7.3). Other event types: unconstrained
> Scope examples: `project` / `task:T<N>` / `subinstance:<path>` / `lit` — extend as needed
> Event type examples: `milestone` / `action` / `discovery` / `decision` / `decision_reversal` / `structural` / `settled` — extend as needed
> Literature settlement (CONVENTIONS §10) appends, e.g.: `YYYY-MM-DD · lit · settled · <DOI→item_key>… · resolved_model · backlog:[…]`

- **YYYY-MM-DD** · project · structural · initial bootstrap from doc-spine template

---

## Sub-instance roster

> Detailed configuration lives inside each sub-instance (`inputs/`, `sbatch/`, `STATUS.yaml` if present). This roster records only `path → role → status`.

| Path | Role | Status |
|---|---|---|
| <e.g., `01_<stage>/<run_name>/`> | <e.g., baseline calculation> | <status> |
