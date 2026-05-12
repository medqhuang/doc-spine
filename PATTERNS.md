# PATTERNS · Anti-Bloat Patterns + Failure Modes

> Documented patterns observed in real projects. Read before adding new rules to entry files (CLAUDE.md, README.md, SPEC.md).

English (Chinese translation: contributions welcome)

---

## Part 1 · Three failure modes (observed in the wild)

These are not hypothetical. Each has occurred in real projects and triggered a structural fix.

### Failure 1 · Project becomes uncontrollable

**Symptom**: 150 markdown files accumulated; prose status drifts across files; no one can tell which version is "current."

**Root cause**: No single-source discipline. Status updates accumulated as new paragraphs in 6+ files in parallel.

**Fix**: Introduce `STATE.md` as the single status source. Strip status prose from all other files. (Migration takes 1-2 hours; see [HOWTO.md §5](HOWTO.md).)

---

### Failure 2 · Sub-instance documentation goes stale silently

**Symptom**: A sub-instance's `CLAUDE.md` / `meta/README.md` still says "waiting for upstream X to complete," but X has been complete for weeks. The sub-instance has been running production for days.

**Root cause**: Status prose leaked into sub-instance docs. The sub-instance's local description was treated as a place to record current state, instead of pointing to the central `STATE.md`.

**Fix**: Sub-instance docs become **stable architecture descriptions only** (role, sbatch chain, interfaces). Status fields move to `STATE.md §sub-instance roster` or a sub-instance-local `STATUS.yaml`. Sub-instance prose never mentions current status — it points upward.

---

### Failure 3 · Entry file develops inter-section redundancy

**Symptom**: `CLAUDE.md` has §Read order + §Project facts + §5 content types table — all three sections describe the same content-to-location mapping in different framings.

**Root cause**: When solving "cross-file duplication" (the primary architecture goal), there is a natural instinct to add **redundancy within a single file** to make it "comprehensive." This violates the architecture's own principle at a sub-file level.

**Why this is the most important failure**: The anti-entropy principle must be **applied recursively** — across files **and within files**. A 30-line entry doc with three overlapping sections is just as broken as 6 files with overlapping prose.

**Fix**: Audit entry files for inter-section duplication. Each section must answer a question the others don't answer.

---

## Part 2 · Anti-bloat patterns (preserve evolution space)

These are micro-patterns that prevent the architecture from rigidifying as it evolves.

### Pattern 1 · Don't harden enums

**Symptom**: Listing categories with words like "dictionary," "allowed values," or visually closed enum-like tables. Future scenarios then can't introduce a new category without feeling like a violation; the AI awkwardly forces new content into one of the existing categories.

**Cause**: Premature crystallization. The author at time T wrote down what categories existed at time T and unconsciously treated the list as closed.

**Fix**: Replace "dictionary" or "enum" with **"examples — extend as needed."** Make it explicit that the list is open.

**Example**: In `STATE.md`, the scope field used to read "scope dictionary: `task:T<N> milestone/action/discovery` / `decision_reversal` / `structural`". This subtly forbade new scope types like `paper_draft` or `reviewer_response`. Changed to "scope examples: ... (extend as needed)."

---

### Pattern 2 · Precedent boundary annotations

**Symptom**: A special-case rule is added to an entry file (e.g., "STATE.md has its own writing discipline"). Six months later, someone reasons: "If STATE has special rules in the entry file, why not TASKS? Why not reports? Why not ROADMAP?" The entry file balloons.

**Cause**: The special-case rule was added without an explicit boundary marker. Future contributors (human or AI) assume the precedent applies generally.

**Fix**: When adding a special-case rule, **end the section with a one-line blockquote** stating: "This section is a special case for X (reason: Y). Similar rules for other files belong in CONVENTIONS.md, not here."

**Example**: In `CLAUDE.md`, after the §STATE writing discipline section:
> *This section is a STATE.md special case (high-frequency append-only is architecturally critical). Writing details for other files (TASKS / reports / ROADMAP) belong in CONVENTIONS.md, not here.*

---

### Meta-principle · Missing boundary is more dangerous than missing rule

The most surprising lesson from these patterns:

> **In an evolving architecture, the most dangerous omission is not a missing rule — it is a missing boundary.**

You can always add a rule when a real problem demands it. But a special-case rule without an explicit boundary will, with high probability, be **generalized** by future contributors. Two or three such generalizations and the entry file is no longer lean.

**Every special-case rule should carry its own fence**. The fence costs one line; missing it costs a refactor.

---

## Part 3 · How to detect drift early

Periodic checks (low effort, high value):

### Check 1 · Inter-section redundancy in entry files

For `CLAUDE.md`, `README.md`, and any "manifesto" file:
- Read top to bottom. Ask: "Does §B say anything that §A didn't already say?"
- If §B is a rewording of §A in a different framing, merge or remove.

### Check 2 · Status prose leak

Grep across all files for status indicators (`in-progress`, `pending`, `2026-XX-XX update`, `SUPERSEDED`, `当前状态`, etc.).
- If found in any file **other than STATE.md** or `STATE.yaml`, that's a leak. Fix by moving to STATE log.

### Check 3 · Number-of-locations-per-event metric

When an event occurs (job complete, decision reversal, status change), count the number of files you update.
- Architecturally healthy: ≤3 files (STATE + maybe one of: TASKS / ROADMAP / report)
- Drift signal: ≥4 files. Audit which prose update was redundant.

### Check 4 · Frontmatter drift

For files with YAML frontmatter (`STATE.md`, sub-instance `STATUS.yaml`):
- Parse the frontmatter periodically; verify field values match the prose body.
- Drift here is silent: frontmatter says `active_task: T2` but body still narrates T1.

---

## Part 4 · When NOT to add a pattern

Following the **"no preventive over-engineering"** principle ([SPEC.md §4](SPEC.md) principle 6):

**Do not** add a new pattern, rule, audit script, or template until a real failure has occurred. The patterns in this file came from observed failures, not speculation. Speculative patterns themselves become bloat.

If you find yourself wanting to add a pattern preemptively, write it down in a personal scratch file. Wait for the failure. If it doesn't occur within 3 months of active use, the pattern was likely not needed.
