# <PROJECT_NAME> · Conventions

> Project-specific naming, git workflow, frozen-instance policy, and the 5-content-type-map (single-source mapping).
> General rules that apply to all your projects: see `~/work/CLAUDE.md` (or equivalent global user instructions).

## 1. Directory layout

```
<PROJECT_NAME>/
├── CLAUDE.md             AI entry (auto-load)
├── README.md             human entry
├── HOWTO.md              (optional) architecture usage guide
├── docs/                 project-level
│   ├── ROADMAP.md        strategy / stages / red lines / DAG / locked decisions
│   ├── CONVENTIONS.md    this file
│   ├── <REGISTRY>.yaml   (optional) pure registry data
│   └── _archive/         historical snapshots (do not delete; do not edit)
├── <library/>            (optional) reusable code library
└── instances/
    ├── <ACTIVE_INSTANCE>/
    │   ├── STATE.md ⭐⭐ status + event log (single source)
    │   ├── README.md     instance entry
    │   ├── docs/TASKS.md task spec (no status)
    │   └── reports/      completion reports
    └── <frozen_baseline>/   .aiignore-enforced read-only (if applicable)
```

## 2. 5 content types · single-source map

| Content | Location |
|---|---|
| State / decisions / events / reversals | `instances/<ACTIVE_INSTANCE>/STATE.md` (append-only event log) |
| Task spec | `instances/<ACTIVE_INSTANCE>/docs/TASKS.md` |
| Completion reports + data trace | `instances/<ACTIVE_INSTANCE>/reports/T<N>_report.md` |
| Strategy / red lines / locked decisions | `docs/ROADMAP.md` |
| Cross-project knowledge | user-level memory (e.g., `~/.claude/projects/<path>/memory/`) |

**No 6th type by default.** Add only on real signal (e.g., a publication-writing phase that needs a canonical numbers registry).

## 3. Frozen-instance policy

`instances/<frozen>/` is **read-only**. Enforced by `.aiignore` file at the directory root.

| Operation | Allowed? |
|---|---|
| Modify content of existing markdown | ❌ |
| Add new INDEX / TOC / merged overview | ✅ |
| Move old markdown to `_legacy/` (byte-level retention) | ✅ |
| Edit README links to adapt to reorganization (navigation only) | ✅ (cautiously) |

## 4. File naming conventions

| Name | Role | Example |
|---|---|---|
| `README.md` | Directory-level navigation (what's here and where to enter) | `instances/<INSTANCE>/README.md` |
| `INDEX.md` | Topic / role / time-based cross-cutting navigation | `<frozen_instance>/artifacts/INDEX.md` |
| `_legacy/` | Frozen / deprecated files, byte-level preserved | `<frozen_instance>/_legacy/` |
| `_archive/` | Project-level historical snapshots (e.g., post-reorganization) | `docs/_archive/` |

## 5. Sub-instance naming

`instances/<material_or_target>/<stage_NN>_<purpose>/<run_identifier>/`

`<run_identifier>` format: `<material>_<variant>_<purpose>_<source>_<host>_<software>_<version_or_date>`

Examples:
- `<target>_bulk_relax_<pp_choice>_<cluster>_<software_version>_<YYYYMMDD>`
- `<target>_dfpt_<mesh>_<cluster>_<software_version>_<YYYYMMDD>`

Each sub-instance contains its own `inputs/`, `sbatch/` or run scripts, `scripts/`, output artifacts, and (if needed) `meta/README.md` describing role + key parameters.

**Completion reports** belong to the **instance level** (`instances/<INSTANCE>/reports/T<N>_report.md`), not inside the sub-instance — because a single task's report often integrates data from multiple sub-instances.

## 6. Domain-specific rules

[Add project-specific conventions here. Examples:
- For DFT projects: pseudo-potential conventions, k-mesh constraints, HPC partition defaults
- For software projects: language style guide pointers, framework conventions, deployment targets
- For research projects: citation format, paper structure constraints, reproducibility checklist]

## 7. Cleanup record

Reorganization operations (file moves, archive operations, structural changes) are recorded via **`git log`** plus the relevant instance `STATE.md` event log (event type `structural`). Past snapshots live in `_reorg_log_<date>/` (rollback artifacts) and `docs/_archive/`.

## 8. Git workflow

### .gitignore

Do not commit:
- Build artifacts / `__pycache__/` / `*.pyc` / `.pytest_cache/`
- Virtual environments / `.venv/`
- OS / editor files (`.DS_Store`, `.idea/`, etc.)
- Domain-specific large objects (e.g., DFT: `WAVECAR`, `CHGCAR`, `*.wfc*`, `*.epmatwp`, `*.hdf5`)
- Reorg snapshots (`_reorg_log_*/`)

### Commit rules

- **Frozen-instance boundary (hard rule)**: do not commit content changes inside `instances/<frozen>/`. Allowed: add new INDEX / move old md to `_legacy/` / add CLAUDE.md / etc.
- **Large file check**: before commit, ensure no files >10 MB are staged outside `.gitignore`
- **Commit message**: terse; describe the **why**, not what (git diff shows what)
- **No force push to main; no `--no-verify` to skip hooks** unless explicitly requested
- **Failed pre-commit hook → fix and create a new commit**, do not `--amend` (amend modifies the prior commit, risking lost work)

## 9. Status

`main` branch only by default. Remote / multi-branch strategy added on demand.
