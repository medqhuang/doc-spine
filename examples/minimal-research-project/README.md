# widget-research

A tiny example project demonstrating the [anti-entropy-docs](../../) architecture in action. The fake "research" is characterizing imaginary widgets — content is illustrative; structure is the point.

```
CLAUDE.md / README.md         entry files
docs/                          ROADMAP / CONVENTIONS
instances/widget-v1/          ★ STATE.md / docs/TASKS.md / reports/
```

30-second orient:

- Current work → [`instances/widget-v1/STATE.md`](instances/widget-v1/STATE.md) ★
- AI rules (auto-loaded) → [`CLAUDE.md`](CLAUDE.md)
- Strategy → [`docs/ROADMAP.md`](docs/ROADMAP.md)
- How the architecture works → [`../../HOWTO.md`](../../HOWTO.md)

## What to look at

This example demonstrates:
- A **STATE.md** with YAML frontmatter (current state), a task graph, an event log with **3 worked events** (one milestone, one reversal, one discovery), and a sub-instance roster
- A **TASKS.md** with **pure spec** (no status) for 3 tasks
- A **completed task report** (`reports/T1_report.md`) with the report-frontmatter pattern
- A **ROADMAP.md** with locked decisions + a red line

Fork this example, replace "widget" with your domain, and you have a working starter.

---
*Adapted from [anti-entropy-docs](https://github.com/<YOUR_USER>/anti-entropy-docs).*
