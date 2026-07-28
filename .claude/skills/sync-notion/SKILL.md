---
name: sync-notion
description: Push CLAUDE.md and companion docs (docs/backlog.md, docs/activity-log.md, docs/design-decisions.md) to their mirrored Notion pages, and check the notion-to-code-sync page for queued ideas from Claude.ai voice/chat sessions. Scoped to projects where CLAUDE.md has a "### Notion workspace" heading — not for a Notion-only project with no such heading (see update-notion-project instead). Use when update-claude finishes a local write, at the start of a new Claude Code session, or when the user says things like "sync notion," "check notion for updates," or "push to notion."
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

## Relationship to update-notion-project

`update-notion-project` (a separate, global skill) does live, targeted
edits directly to Notion content based on conversation — a fundamentally
different write model than this skill's full-replace/local-file-always-wins
approach. The two are incompatible on the same pages: if
`update-notion-project` ever edits one of this project's four mirror
pages, that edit is silently clobbered by the next `sync-notion` push.

The mechanism that keeps them apart is a single inline-code line,
`` `sync-notion:managed` ``, placed right under the `### Notion workspace`
heading in `CLAUDE.md` (see `update-claude`'s companion-docs section /
`project-intake.md`'s Step 5). It mirrors verbatim to the Notion
Instructions page along with everything else, and `update-notion-project`
checks for that exact substring before doing anything. The prose sentence
next to it is for human readers only and can be reworded freely — the
inline-code line is the only part that's load-bearing, specifically so a
routine editorial pass on the readable text can't accidentally disable the
check.

If this project's `CLAUDE.md` has that heading and marker, this skill owns
all writes to that workspace — `update-notion-project` should not run
against it, even if invoked by a phrase like "sync notion" that could
match either skill's trigger. (See Push mechanics step 5 for the
integrity check that keeps this marker from silently going missing.)

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
5. Before pushing `CLAUDE.md` specifically, confirm the
   `` `sync-notion:managed` `` line (see "Relationship to
   update-notion-project" above) is still present in the local file. If
   it's missing, don't silently re-add it and don't silently push anyway
   — tell the user plainly that the marker is gone, that
   `update-notion-project`'s collision check depends on it, and ask
   whether it was removed intentionally (e.g. deliberately un-managing
   this workspace) or should be restored.

## Pull mechanics

1. Fetch the hub page's sub-pages. If no page named `notion-to-code-sync`
   exists yet, create it now — `bootstrap-notion-project`'s standard four
   pages don't include this queue page, so the first sync after a fresh
   bootstrap needs to create it itself. Seed it with:

   ```markdown
   Append-only scratch pad for loose, unprocessed ideas from Claude.ai
   (voice or chat) sessions where no live Claude Code session existed to
   receive them yet.

   **Rules for this page:**
   - Claude.ai only ever *appends* a dated entry here — raw, not rewritten
     into "current state" language, not folded into
     Instructions/Backlog/Design Decisions on its own.
   - Claude Code checks this page at the start of a session and resurfaces
     each unprocessed entry to the user for a decision — it never silently
     ingests an entry into CLAUDE.md/backlog.md/design-decisions.md
     unattended.
   - Once the user confirms how an entry should be handled (turned into a
     prompt, added to Backlog, dropped, etc.), mark the entry
     **[ingested — date]** rather than deleting it, to preserve the audit
     trail.

   _No entries yet._
   ```

   Also add a link to it from the hub page, matching the other four links.
2. Fetch `notion-to-code-sync`. Look for entries not already marked
   `[ingested — date]`.
3. If there are none, say so briefly and move on — don't manufacture a
   drift report out of nothing.
4. If there are unprocessed entries, surface each one to the user
   **verbatim, raw** — exactly as it was appended, not reworded or
   summarized into "current state" language. Never fold an entry into
   `CLAUDE.md`/`backlog.md`/`design-decisions.md` on your own judgment of
   what it "must have meant." You have file access the Claude.ai session
   didn't; use it to check the entry against real repo state, but the
   decision on what to do with it is the user's, not an inference.
5. For each entry, ask what should happen to it: turn into a prompt/plan,
   add directly to Backlog, fold into Design Decisions, or drop it. Wait
   for the user's call per entry — don't batch-decide on their behalf.
6. Once resolved, mark that entry `[ingested — YYYY-MM-DD]` in place
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
  happen, not assume it did. Point them to `docs/notion-mcp-setup.md` for
  the one-time, per-machine connection steps — this is a manual/OAuth
  step that no skill can complete on its own.
- **Never let `update-notion-project` run against this project's four
  mirror pages** — see "Relationship to update-notion-project" above. If
  asked to do something that sounds like `update-notion-project`'s job
  (live-editing Notion content directly) on one of the four mirror pages
  specifically, redirect to this skill's push flow instead.
- Bundle the push into the same approval `update-claude` already secured
  for the local write; bundle pull-surfacing into the same session
  wherever it naturally happens (typically at the top of an
  `update-claude` run, per that skill's drift-check pass) rather than
  requiring a separate standalone invocation every time.

