# widget-research · ROADMAP

> Demo project to illustrate anti-entropy-docs. The "physics" is fake; the structure is real.

## Project · what and why

This pretend project characterizes "widgets" by:
1. Measuring their baseline properties (T1)
2. Stressing them under perturbation (T2)
3. Modeling the stress response (T3)

Final deliverable: a one-page note demonstrating widget linear-response regime.

## Stages

```
Stage 1   : baseline + stress measurement (T1 → T2)
   ↓
Stage 2   : modeling (T3) + write-up
```

## Locked decisions

### Measurement method

- **Choice**: Method A (cheaper, established)
- **Date**: 2026-01-01
- **Rationale**: Method B requires equipment unavailable until Q2; Method A precedent exists for widget-class systems.

### Modeling framework

- **Choice**: Linear response model
- **Date**: 2026-01-01
- **Rationale**: Widget perturbation is expected to be in linear regime (~5% strain); nonlinear extensions deferred to followup.

## Red lines

- **Stress response > 20% nonlinear at low strain** → drop linear model, switch to nonlinear (or abort if scope blows up)

## Project history

Milestones / reversals / state changes → [`instances/widget-v1/STATE.md`](../instances/widget-v1/STATE.md) §event log.

## Entry table

| Looking for | Go to |
|---|---|
| **Live state** | [`../instances/widget-v1/STATE.md`](../instances/widget-v1/STATE.md) ★ |
| Task spec | [`../instances/widget-v1/docs/TASKS.md`](../instances/widget-v1/docs/TASKS.md) |
| Naming / conventions | [`CONVENTIONS.md`](CONVENTIONS.md) |
