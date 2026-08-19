---
active_task: T2
active_subinstance: 01_measure/widget_v1_runA_2026-02-01
in_flight_jobs:
  - {jid: 5001, type: stress measurement, started: "2026-02-15", expected_finish: "2026-02-16"}
next_action: "Wait for jid 5001, then validate stress-vs-strain linearity against the ROADMAP red line."
thesis: "Widget stress response is linear within ±10% strain · confidence: tentative (direct test = T2, jid 5001) · support: event 2026-02-08 (S-2024 re-analysis) · lineage: quadratic →[2026-02-08]→ linear (event log)"
last_updated: 2026-02-15
---

# widget-v1 · State

> Single source of truth for state + events. Current snapshot lives in YAML frontmatter; task graph, event log, and sub-instance roster live below. Spec -> [`docs/TASKS.md`](docs/TASKS.md); strategy -> [`../../docs/ROADMAP.md`](../../docs/ROADMAP.md).

## Task graph

| Task | Status | One-line description |
|---|---|---|
| T1 | done 2026-02-10 | Baseline widget properties |
| T2 | in-flight (jid 5001) | Stress response under perturbation |
| T3 | blocked by T2 | Linear-response model fit |

## Event log (append-only, one line per event)

> Format: `YYYY-MM-DD · <scope> · <event type> · summary + key data (± uncertainty) + consequence / what remains open`

- 2026-01-01 · project · structural · bootstrap from doc-spine example
- 2026-01-05 · project · decision · locked Method A because Method B equipment is unavailable until Q2
- 2026-01-05 · project · decision · modeling framework: quadratic response model, per early widget literature
- 2026-01-20 · task:T1 · action · baseline run launched (jid 4990)
- 2026-01-27 · task:T1 · milestone · baseline completed (jid 4990), key result P = 42.1 ± 0.3, within precedent range. ref: reports/T1_report.md
- 2026-02-03 · task:T1 · discovery · detector channel 7 drifted 2%; T2 must use channels 1-6 only. ref: reports/T1_report.md §2
- 2026-02-08 · project · decision_reversal · modeling framework quadratic → linear: S-2024 precedent re-analysis shows curvature <2% up to ±10% strain, below our σ; T3 switches to linear fit. prior_decision: 2026-01-05
- 2026-02-10 · task:T1 · milestone · T1 closed; all completion criteria met; unblocks T2
- 2026-02-14 · task:T2 · action · stress measurement input prepared in `01_measure/widget_v1_runA_2026-02-01`
- 2026-02-15 · task:T2 · action · stress measurement launched (jid 5001)

## Sub-instance roster

| Path | Role | Status |
|---|---|---|
| `01_measure/widget_v1_runA_2026-02-01/` | T1 baseline + T2 stress setup | T1 done; T2 in-flight |

## Edit permissions reminder

- State changes / decisions / events -> here (append-only event log + frontmatter overwrite)
- No live status prose in TASKS / reports / sub-instance docs
- History corrections -> `git revert` + new event
