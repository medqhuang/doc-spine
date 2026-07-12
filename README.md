# doc-spine

> A documentation architecture for **long-running, AI-collaborative projects** — designed to prevent the slow entropy accumulation that turns "well-documented" projects into unreadable prose graveyards.

---

## The problem this solves

You start a multi-month research / engineering project with an AI coding agent. You document everything carefully. Six months in, you have **150 markdown files**, status updates are scattered across 6 locations, every decision reversal leaves a `SUPERSEDED` banner, and **you no longer trust your own documentation** because you can't tell what's current.

This is a known pattern. It comes from a specific cause:

> **Status updates accumulate as prose in multiple files**, instead of being written once to a single source.

This repo specifies an architecture that prevents that accumulation. It is **not new** — it borrows from event sourcing (software), state/spec separation (systems engineering), and append-only logs (databases). What's new is **applying these patterns specifically to AI-collaborative documentation**, with explicit anti-bloat patterns and a real failure→success case study.

---

## The shape of the solution

Three coupled mechanisms — **Single Source**, **One-Way** ownership, **Append-Only** event log — applied to **5 canonical content types** (state, task spec, completion report, strategy, cross-project knowledge). Each type lives in exactly one file; everything else points to it.

Research / writing projects can opt into a **6th (literature)** type — bibliographic identity in Zotero, project-side evidence & settlement audit in STATE; see [`templates/CONVENTIONS.md` §10](templates/CONVENTIONS.md).

Heavily AI-driven projects can further opt into the **author layer** — two downstream types written at closeout that give the human owner back what execution artifacts can't: a **tutorial** ("what is this project", image-first teaching) and a **first-author dossier** ("can I sign this" — claim-indexed evidence grades, retraction conditions, weakness table, reconstruction oral exam). See [SPEC.md §3](SPEC.md) and the two charters in [`templates/`](templates/).

Nothing should live in two places. If two files describe the same status, one is stale by definition.

→ Full definitions and rationale: [SPEC.md §2-3](SPEC.md).

---

## Quick decision: is this for you?

| Project profile | Use this architecture |
|---|---|
| Multi-session / multi-month / AI as active collaborator / multiple sub-tasks / decision reversals possible | ✅ Yes |
| Single session / single task / simple workflow / pure human-authored | ❌ Overkill — plain README + git log is enough |

---

## What this is *not*

doc-spine solves **one** problem: state drift across prose files in long-running AI-collaborative projects.

Not a substitute for project management (Linear, Jira), doc generators (Sphinx, mkdocs), AI context engineering (memory / RAG), or code architecture patterns.

---

## What's in this repo

| File | What it is |
|---|---|
| [`README.md`](README.md) | This file — 5-minute pitch |
| [`SPEC.md`](SPEC.md) | Full specification: mechanisms, content types, design principles, failure modes, anti-bloat patterns, file layout |
| [`HOWTO.md`](HOWTO.md) | Operational guide: lookup tables, 3 daily scenarios, new-project + migration checklists |
| [`case-studies/`](case-studies/) | Three anonymized case studies — two failures (doc bloat / pivot-phase scratch sprawl), one success (state-sourced) |
| [`templates/`](templates/) | Copy-paste skeleton files (CLAUDE.md, STATE.md, TASKS.md, etc.) — incl. `lit-settle.reference.md` (optional literature settlement via Zotero) and the author-layer charters `TUTORIAL_WRITING_STANDARD.md` / `DOSSIER_WRITING_STANDARD.md` (zh) |
| [`examples/minimal-research-project/`](examples/minimal-research-project/) | A tiny filled-out project showing the skeleton in action |

---

## Quickstart (new project, 30 minutes)

```bash
project=my-new-project
instance=main

mkdir -p "$project/docs" "$project/instances/$instance/docs" "$project/instances/$instance/reports"
cp doc-spine/templates/CLAUDE.md "$project/CLAUDE.md"
cp doc-spine/templates/README.md "$project/README.md"
cp doc-spine/templates/HOWTO.md "$project/HOWTO.md"
cp doc-spine/templates/ROADMAP.md "$project/docs/ROADMAP.md"
cp doc-spine/templates/CONVENTIONS.md "$project/docs/CONVENTIONS.md"
cp doc-spine/templates/STATE.md "$project/instances/$instance/STATE.md"
cp doc-spine/templates/INSTANCE_README.md "$project/instances/$instance/README.md"
cp doc-spine/templates/TASKS.md "$project/instances/$instance/docs/TASKS.md"

# Then fill placeholders: <PROJECT_NAME>, <ACTIVE_INSTANCE>, <PROJECT_PATH>, etc.
cd "$project" && git init && git add -A && git commit -m "Bootstrap with doc-spine"
```

See [HOWTO.md §New project checklist](HOWTO.md) for full steps.

---

## Philosophy in one sentence

> **Each event is written once; one writing lands in one location; one location carries one content type.**

If you remember nothing else, remember this. Everything else in this repo is derived.

---

## Status

Version 0.4 (STATE events gain **claim semantics** — a claim-bearing event records the current best-supported understanding, not an absolute fact: key data with its uncertainty + what would overturn the conclusion, still one line; reports close with a claims-at-completion ledger. See [SPEC §7.3](SPEC.md). v0.3 added the optional **author layer** — tutorial + first-author dossier, two downstream content types that restore human mastery and authorship in AI-driven projects; charters in [`templates/`](templates/), currently zh. v0.2 added the optional 6th type — **literature settlement** via Zotero; see [`templates/CONVENTIONS.md` §10](templates/CONVENTIONS.md)). Derived from multi-month computational physics projects — all featuring **HPC compute paired with local AI-assisted coding**. Three case studies: an initial failure (doc bloat), a state-sourced success, and a pivot-phase scratch-sprawl failure caught inside an otherwise-healthy project (see [`case-studies/`](case-studies/)). The architecture is stable in this workflow; effectiveness in other domains (web / data / ML / team software) is **unverified** — cross-domain case studies welcome.

## Contributing

Issues / discussions welcome. PRs especially welcome for:
- Additional case studies (anonymized failures or successes)
- Translations (zh / English / others)
- Tooling (audit scripts, template generators) — kept minimal on purpose; propose before building

## License

MIT — see [LICENSE](LICENSE). Templates and methodology are free to use in any project, commercial or otherwise, without attribution required (though appreciated).
