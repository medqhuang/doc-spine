# widget-research · Conventions

> Minimal example conventions. Real projects should expand this only when a real need appears.

## 1. Directory layout

```
widget-research/
├── CLAUDE.md
├── README.md
├── docs/
│   ├── ROADMAP.md
│   └── CONVENTIONS.md
└── instances/widget-v1/
    ├── STATE.md
    ├── docs/TASKS.md
    └── reports/T1_report.md
```

## 2. Content map

| Content | Location |
|---|---|
| Live state / decisions / reversals | `instances/widget-v1/STATE.md` |
| Task spec | `instances/widget-v1/docs/TASKS.md` |
| Completion report + data trace | `instances/widget-v1/reports/T<N>_report.md` |
| Strategy / red lines / locked decisions | `docs/ROADMAP.md` |

## 3. Demo policy

- Keep `TASKS.md` status-free.
- Keep reports as data trace, not running commentary.
- Use `STATE.md` event log for all state transitions.
