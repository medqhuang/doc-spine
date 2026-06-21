# Reference implementation: literature settlement (Claude Code × Zotero)

> `CONVENTIONS.md §10` is the contract (what); this file is one implementation (how).
> Swap the environment and re-implement against the contract — §10 does not change.
> Environment: Claude Code + Zotero MCP (Web API) + git.

## Config
- `lit_settle_model` — the settle subagent's model **tier** (light / balanced / strict), mapped to whatever Claude model is current at the time; never a hardcoded id.
- citation-scope docs — STATE events and `reports/` (by type, not guessed from filename).

## Boundary step (main agent — Task-completion step 4)
1. Locate the cursor: find the last `· lit · settled ·` line in the STATE event log. None → bootstrap: scan all citation-scope docs.
2. Take the increment: events appended after that line + the reports they `ref:`.
3. Extract citations: parse DOI/arXiv in each doc (normalize: strip DOI URL prefix / trailing punctuation, fold arXiv version); recall fills `decision_ref`, load-bearing-or-not, location.
4. New citations → spawn the settle subagent (pass the hot-list); none → skip.
5. Receive the receipt → append a `lit · settled` event (audit + role + cursor).

## Settle subagent (isolation chamber)
- Model: read the `lit_settle_model` tier; record the resolved model in the receipt.
- Tools: Zotero MCP (no PDF reading). Dirty context (schemas / query round-trips) stays here, never returned to the main agent.
- Steps:
  1. Work-merge: arXiv ↔ journal DOI count as one work only when metadata clearly links them (prefer journal DOI; arXiv as alias). Ambiguous → backlog; no destructive merge/delete.
  2. Dedup: search Zotero by normalized id; if the item exists, reuse it but still append the new `decision_ref → role` relation (identity dedup must not swallow a new role).
  3. Add: accept only DOI / canonical arXiv ids; author-year / no acceptable id / failure → backlog with a reason (e.g. `identifier_missing`). No guess-matching.
  4. (No PDF reading in settlement.) Attribution/role is taken from the main agent's hot-list (judged from `papers_md/`), not re-derived here.
  5. Receipt: `{item_key, action(new|existing|backlog|failed), role, decision_ref, resolved_model, unresolved:[{surface, ref, reason}]}`.

## Deep reading (separate from settlement)
Deep reading uses marker, not Zotero `read_pdf`:
```
Zotero (imported PDF) --get_attachment_path / file--> markerize --> papers_md/<name>.md
```
- Zotero = cross-project identity + PDF home; `papers_md/` = project-local marker deep-read cache (high-quality text / figures).
- Attribution (`decision_ref` / role) is judged by the main agent while reading `papers_md`, then passed into ④'s hot-list. ④ never opens PDFs.

## Boundary
- Assumes sessions write STATE alternately (not high-frequency concurrent append). If concurrent settlement is introduced: add an event-log cut (record `cut_event_id` at start, write it into the settled event at end) + serialize Zotero writes.
- backlog is currently a locatable list + manual handling; auto-retry / `resolved_from` relations are deferred until actually needed (SPEC §9.3).
