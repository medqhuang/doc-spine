# widget-research

A tiny example project demonstrating the [doc-spine](../../) architecture in action. The fake "research" is characterizing imaginary widgets; the content is illustrative, but the document structure is real.

```
CLAUDE.md / README.md / HOWTO.md     entry files
docs/                                 ROADMAP / CONVENTIONS
instances/widget-v1/                  STATE.md / docs/TASKS.md / reports/
```

30-second orient:

- Current work -> [`instances/widget-v1/STATE.md`](instances/widget-v1/STATE.md)
- AI rules -> [`CLAUDE.md`](CLAUDE.md)
- Strategy -> [`docs/ROADMAP.md`](docs/ROADMAP.md)
- How the architecture works -> [`../../HOWTO.md`](../../HOWTO.md)

## What to look at

This example demonstrates:
- A `STATE.md` with YAML frontmatter, a task graph, an append-only event log, and a sub-instance roster
- A `TASKS.md` with pure task spec and no live status prose
- A completed task report with data trace and downstream interface
- A `ROADMAP.md` with locked decisions and a red line

Fork this example, replace "widget" with your domain, and you have a working starter.

---
*Adapted from [doc-spine](https://github.com/<YOUR_USER>/doc-spine).*
