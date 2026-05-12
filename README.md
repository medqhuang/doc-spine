# anti-entropy-docs

> A documentation architecture for **long-running, AI-collaborative projects** — designed to prevent the slow entropy accumulation that turns "well-documented" projects into unreadable prose graveyards.

[中文](README.zh.md) | English

---

## The problem this solves

You start a multi-month research / engineering project with an AI coding agent. You document everything carefully. Six months in, you have **150 markdown files**, status updates are scattered across 6 locations, every decision reversal leaves a `SUPERSEDED` banner, and **you no longer trust your own documentation** because you can't tell what's current.

This is a known pattern. It comes from a specific cause:

> **Status updates accumulate as prose in multiple files**, instead of being written once to a single source.

This repo specifies an architecture that prevents that accumulation. It is **not new** — it borrows from event sourcing (software), state/spec separation (systems engineering), and append-only logs (databases). What's new is **applying these patterns specifically to AI-collaborative documentation**, with explicit anti-bloat patterns and a real failure→success case study.

---

## 5-minute overview

The architecture has **3 anti-entropy mechanisms** and **5 content types**.

### 3 mechanisms

| Mechanism | Rule |
|---|---|
| **Single Source** | Each content type lives in exactly one location. Other files use pointers, never duplicate prose. |
| **One-Way** | Cross-file references are one-way. Downstream reads upstream; does not write back. |
| **Append-Only** | Events / decisions / reversals never modify past entries. Corrections go through git revert + new event. |

### 5 content types

| Content | Lives in | Format |
|---|---|---|
| State / decisions / events / reversals | `<instance>/STATE.md` (append one line per event) | MD + YAML frontmatter |
| Task spec (input/steps/output/criteria/risks) | `<instance>/docs/TASKS.md` | MD |
| Completion report + data trace | `<instance>/reports/T<N>_report.md` (write once, then read-only) | MD |
| Strategy / red lines / locked decisions / DAG | `docs/ROADMAP.md` | MD |
| Cross-project knowledge (HPC quirks, tool gotchas) | `~/.claude/projects/.../memory/` | MD |

**Nothing should ever live in two places.** If two files describe the same status, one of them is stale.

---

## Quick decision: is this for you?

| Project profile | Use this architecture |
|---|---|
| Multi-session / multi-month / AI as active collaborator / multiple sub-tasks / decision reversals possible | ✅ Yes |
| Single session / single task / simple workflow / pure human-authored | ❌ Overkill — plain README + git log is enough |

---

## What's in this repo

| File | What it is |
|---|---|
| [`README.md`](README.md) | This file — 5-minute pitch |
| [`SPEC.md`](SPEC.md) | Full specification: 3 mechanisms, 5 content types, 7 design principles, file layout |
| [`HOWTO.md`](HOWTO.md) | Operational guide: lookup tables, 3 daily scenarios, new-project + migration checklists |
| [`PATTERNS.md`](PATTERNS.md) | Anti-bloat patterns + 3 documented failure modes |
| [`case-studies/`](case-studies/) | Two anonymized case studies — one failure (doc bloat), one success (state-sourced) |
| [`templates/`](templates/) | Copy-paste skeleton files (CLAUDE.md, STATE.md, TASKS.md, etc.) |
| [`examples/minimal-research-project/`](examples/minimal-research-project/) | A tiny working example you can fork to see the architecture in action |

---

## Quickstart (new project, 30 minutes)

```bash
# 1. Copy templates into your new project
cp -r anti-entropy-docs/templates/* my-new-project/

# 2. Open templates and fill in placeholders (search for <PROJECT_NAME>, <INSTANCE_NAME>, etc.)
# 3. Commit
cd my-new-project && git init && git add -A && git commit -m "Bootstrap with anti-entropy-docs"
```

See [HOWTO.md §New project checklist](HOWTO.md) for full steps.

---

## Philosophy in one sentence

> **Each event is written once; one writing lands in one location; one location carries one content type.**

If you remember nothing else, remember this. Everything else in this repo is derived.

---

## Status

Version 0.1 (initial public release). Derived from real-world experience in two research projects (one failed, one succeeded — see [`case-studies/`](case-studies/)). The architecture is **stable enough to use** but the documentation will iterate based on user feedback.

## Contributing

Issues / discussions welcome. PRs especially welcome for:
- Additional case studies (anonymized failures or successes)
- Translations (zh / English / others)
- Tooling (audit scripts, template generators) — kept minimal on purpose; propose before building

## License

MIT — see [LICENSE](LICENSE). Templates and methodology are free to use in any project, commercial or otherwise, without attribution required (though appreciated).
