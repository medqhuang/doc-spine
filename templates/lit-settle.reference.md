# Reference implementation: literature settlement (Claude Code × Zotero)

> `CONVENTIONS.md §10` is the contract (what); this file is one implementation (how).
> Swap the environment and re-implement against the contract — §10 does not change.
> Environment: Claude Code + Zotero MCP (**Web API only** — `ZOTERO_LOCAL=false`) + git.
> Binding constraint, not a note: **every step below must hold with Zotero desktop closed.** If a step needs a local-mode-only tool, it is the wrong step — find the Web API equivalent. (Zotero's local API is read-only anyway, so all writes here already go over the Web API.)

## Config
- `lit_settle_model` — the settle subagent's model **tier** (light / balanced / strict), mapped to whatever Claude model is current at the time; never a hardcoded id.
- citation-scope docs — STATE events and `reports/` (by type, not guessed from filename).

## Boundary step (main agent — Task-completion step 4)
1. Locate the cursor: find the last `· lit · settled ·` line in the STATE event log. None → bootstrap: scan all citation-scope docs.
2. Take the increment: events appended after that line + the reports they `ref:`.
3. Extract citations: parse DOI/arXiv in each doc (normalize: strip DOI URL prefix / trailing punctuation, fold arXiv version) + note where each was found. Grep only — nothing here is recalled.
4. New citations → spawn the settle subagent (pass the hot-list); none → skip.
5. Receive the receipt → append a `lit · settled` event (audit + cursor).

## Settle subagent (isolation chamber)
- Model: read the `lit_settle_model` tier; record the resolved model in the receipt.
- Tools: Zotero MCP (no PDF reading). Dirty context (schemas / query round-trips) stays here, never returned to the main agent.
- Steps:
  1. Work-merge: arXiv ↔ journal DOI count as one work only when metadata clearly links them (prefer journal DOI; arXiv as alias). Ambiguous → backlog; no destructive merge/delete.
  2. Dedup: search Zotero by normalized id; if the item exists, reuse it and report `existing` — nothing further to attach.
  3. Add: accept only DOI / canonical arXiv ids; author-year / no acceptable id / failure → backlog with a reason (e.g. `identifier_missing`). No guess-matching.
  4. No PDF reading, and no judgement about what a ref supports — this subagent decides identity only.
  5. Receipt: `{item_key, action(new|existing|backlog|failed), resolved_model, unresolved:[{surface, ref, reason}]}`.

## Deep reading (separate from settlement)
Deep reading uses marker, not Zotero `read_pdf`:
```
Zotero (attachment key) --download to a stable dir--> markerize --> papers_md/<name>.md
```
- Fetch the attachment **by key into a stable path** (not a temp dir — `papers_md/` must stay reproducible from it). Web API: pyzotero `zot.dump(attachment_key, filename, path=<dir>)`; the MCP wraps this as `client.download_attachment_file(key, dir, filename)`, which tries local → WebDAV → Web API and reports which source it used.
- **Do not** use the `zotero_get_attachment_path` tool: it is local-mode only (a cloud attachment has no local path), and reaching for it is exactly how this flow silently becomes desktop-only.
- Precondition: attachments are `imported_file` **and** file-synced (Zotero storage or WebDAV). `linked_file` attachments have no bytes on the server — re-import them, or that ref stays deep-read-only on the machine holding the file.
- Zotero = cross-project identity + PDF home; `papers_md/` = project-local marker deep-read cache (high-quality text / figures).
- What a deep read concludes goes where conclusions go — the report and its claims ledger. It does not come back into settlement.

## Boundary
- Assumes sessions write STATE alternately (not high-frequency concurrent append). If concurrent settlement is introduced: add an event-log cut (record `cut_event_id` at start, write it into the settled event at end) + serialize Zotero writes.
- backlog is currently a locatable list + manual handling; auto-retry / `resolved_from` relations are deferred until actually needed (SPEC §9.3).
