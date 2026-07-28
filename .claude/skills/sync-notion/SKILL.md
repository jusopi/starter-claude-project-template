---
name: sync-notion
description: Push CLAUDE.md and companion docs (docs/backlog.md, docs/activity-log.md, docs/design-decisions.md) to their mirrored Notion pages, and check the notion-to-code-sync page for queued ideas from Claude.ai voice/chat sessions. Use when update-claude finishes a local write, at the start of a new Claude Code session, or when the user says things like "sync notion," "check notion for updates," or "push to notion."
---

# Sync with Notion

This project's Notion workspace holds four **mirror pages** (Instructions,
Backlog, Activity Log, Design Decisions) plus one **queue page**
(`notion-to-code-sync`). This skill owns the mechanics of keeping both
sides honest. `update-claude` decides *what* changed in the local files;
this skill decides *how* that reaches Notion and *how* Notion's queue
reaches back into the repo.

The Notion workspace's hub page URL (and each sub-page's URL) should be
recorded in `CLAUDE.md` under a `### Notion workspace` heading — see
`update-claude`'s companion-docs section. If that heading is missing or
looks stale, say so and ask before guessing at page URLs.

## Two flows, never blended

**Push (repo → Notion).** The four local files are the master copy.
Pushing means overwriting each mirror page's content with the current
exact text of its matching local file — full `replace_content`, not a
targeted edit, not a summary, not a reorganization. If a mirror page looks
like it needs "cleaning up" or restructuring for Notion's format, that
urge is the wrong instinct here — verbatim is the point. Never read a
mirror page as an input to this flow; it is a write-only target.

**Pull (Notion → repo), queue only.** The *only* Notion content ever read
back into the repo is unprocessed entries on `notion-to-code-sync`. Never
read the four mirror pages for pull purposes — they're pushed *from* the
repo, so reading them back would just be reading a copy of local state
back into itself, and any manual edit made directly on a mirror page
should never survive the next push anyway.

## Push mechanics

1. Confirm the local write this session (via `update-claude`) has already
   been approved and made.
2. For each of the four files that changed, fetch the matching Notion page
   and call `replace_content` with the file's exact current text. Do this
   as one batch, not interleaved with other Notion calls.
3. Append a changelog entry to `docs/activity-log.md` (and therefore to
   the mirrored Activity Log page once pushed) in the form:
   `### YYYY-MM-DD — Notion sync` followed by one line noting which files
   were pushed. This is the sync marker both sides can check to know
   what's actually mirrored as of when.
4. This entire push happens under the **same go-ahead** as the local
   write `update-claude` already obtained — don't ask a second time for
   permission to push what was just approved to write locally.

## Pull mechanics

1. Fetch `notion-to-code-sync`. Look for entries not already marked
   `[ingested — date]`.
2. If there are none, say so briefly and move on — don't manufacture a
   drift report out of nothing.
3. If there are unprocessed entries, surface each one to the user
   **verbatim, raw** — exactly as it was appended, not reworded or
   summarized into "current state" language. Never fold an entry into
   `CLAUDE.md`/`backlog.md`/`design-decisions.md` on your own judgment of
   what it "must have meant." You have file access the Claude.ai session
   didn't; use it to check the entry against real repo state, but the
   decision on what to do with it is the user's, not an inference.
4. For each entry, ask what should happen to it: turn into a prompt/plan,
   add directly to Backlog, fold into Design Decisions, or drop it. Wait
   for the user's call per entry — don't batch-decide on their behalf.
5. Once resolved, mark that entry `[ingested — YYYY-MM-DD]` in place
   (edit, don't delete) so the audit trail persists. This edit is the one
   exception to "never write to Notion outside a push" — it's queue
   bookkeeping, not mirror content.

## Hard rules

- **Never write to the four mirror pages from anything other than a full,
  verbatim push of the matching local file.** No partial edits, no
  Notion-side "fixes," no reorganizing content on the way in.
- **Never treat a mirror page as a source of truth.** If a mirror page's
  content doesn't match the local file, the local file wins, silently —
  the next push corrects Notion. Don't ask the user to reconcile a
  difference that only exists because Notion drifted from its own copy.
- **Never auto-ingest a `notion-to-code-sync` entry.** Every entry gets
  surfaced for an explicit decision, every time, no exceptions for
  entries that "obviously" belong somewhere.
- **If Notion access isn't configured** (no Notion MCP connection
  available from the Claude Code side), say so plainly and skip the sync
  rather than silently no-op-ing — the user should know sync didn't
  happen, not assume it did.
- Bundle the push into the same approval `update-claude` already secured
  for the local write; bundle pull-surfacing into the same session
  wherever it naturally happens (typically at the top of an
  `update-claude` run, per that skill's drift-check pass) rather than
  requiring a separate standalone invocation every time.
