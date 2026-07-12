# widget-research · ROADMAP

> Demo project to illustrate doc-spine. The "science" is fake; the structure is real.

## Project · what and why

This pretend project characterizes widgets by:
1. Measuring their baseline properties (T1)
2. Stressing them under perturbation (T2)
3. Modeling the stress response (T3)

Final deliverable: a one-page note demonstrating the widget linear-response regime.

## Strategy / stages

```
Stage 1 : baseline + stress measurement (T1 -> T2)
   ↓
Stage 2 : modeling (T3) + write-up
```

## Locked decisions

### Measurement method

- **Choice**: Method A (cheaper, established)
- **Date**: 2026-01-01
- **Rationale**: Method B requires equipment unavailable until Q2; Method A precedent exists for widget-class systems.

### Modeling framework

- **Choice**: Linear response model (reversed from quadratic — see STATE event 2026-02-08)
- **Date**: 2026-02-08 (initial quadratic choice: 2026-01-05)
- **Rationale**: early widget literature suggested a quadratic term, but S-2024 precedent re-analysis shows curvature <2% up to ±10% strain — below our resolution. Nonlinear extensions deferred.

## Red lines

- **Stress response >20% nonlinear at low strain**: stop T3, update `STATE.md`, and revisit the modeling framework.

## Project history

Milestones / reversals / state changes -> [`../instances/widget-v1/STATE.md`](../instances/widget-v1/STATE.md) §event log.

## Entry table

| Looking for | Go to |
|---|---|
| Live state | [`../instances/widget-v1/STATE.md`](../instances/widget-v1/STATE.md) |
| Task spec | [`../instances/widget-v1/docs/TASKS.md`](../instances/widget-v1/docs/TASKS.md) |
| Naming / conventions | [`CONVENTIONS.md`](CONVENTIONS.md) |
