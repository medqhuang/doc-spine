# <ACTIVE_INSTANCE> · scratch/ INDEX — non-canonical working drafts

> Catalog of **scratch** docs (SPEC §8.4): intermediate exploration artifacts — not task-spec, not report, not strategy.
> **Non-canonical**: nothing here is a single source. Any content that becomes authoritative must **promote** to STATE / ROADMAP / a report, leaving a pointer here.
> **Status lives in this INDEX** (active / superseded), never in a doc's body (preserves the no-status-in-doc rule, SPEC §8.2).
> Superseded drafts are **kept in place** (not deleted) to preserve committed audit links; only their INDEX row moves.

## ACTIVE — live drafts (none is an authority)

| doc | sub-type | promotes-to / feeds-into |
|---|---|---|
| `<name>.md` | <derivation-note / audit / synthesis / analysis / reframe-proposal / probe-plan> | <target STATE field / ROADMAP §decision / report — or "TBD"> |

## SUPERSEDED — absorbed or abandoned (kept in place for audit links)

| doc | sub-type | superseded-by |
|---|---|---|
| `<name>.md` | <sub-type> | → <report / decision that absorbed it> |

---

> **Maintenance**: new draft → add an ACTIVE row, pick a sub-type, name its promotes-to target. When a report or decision absorbs it → move the row to SUPERSEDED with the pointer.
> **Health check (SPEC §10)**: every ACTIVE row must have a promotes-to target and must not be cited as an authority anywhere. An unpromoted authority is the only drift signal — directory size is not.
