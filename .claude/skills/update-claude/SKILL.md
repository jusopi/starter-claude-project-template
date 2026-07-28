---
name: update-claude
description: Update CLAUDE.md (and companion docs like docs/activity-log.md, docs/backlog.md) with new project context or decisions. Use when the user says things like "update CLAUDE.md", "sync the CLAUDE.md", or "add that to CLAUDE.md", or at the end of a session/after a major decision to offer an update.
model: claude-haiku-4-5-20251001
---

# Update CLAUDE.md

`CLAUDE.md` is a living document. There are two ways an update happens:

**User-initiated:** if the user says something like *"update CLAUDE.md"*, *"sync the CLAUDE.md"*, or *"add that to CLAUDE.md"*, treat it as a direct instruction — summarize what changed, propose the specific edit, and write it once confirmed.

**Claude-solicited:** at the end of a session, or after a major decision/milestone, ask: *"Want me to update CLAUDE.md with what we decided/built today?"* Wait for a yes before writing anything. Don't solicit more than once per session unless something changed after the first ask.

## Hard rules

- Never write to `CLAUDE.md` without an explicit go-ahead from the user first. To make that go-ahead real rather than a formality, switch to plan mode (`EnterPlanMode`) before drafting the update: present the proposed additions/removals/edits as a plan and let the normal plan-approval flow (`ExitPlanMode`) gate the actual write. Don't edit `CLAUDE.md` directly outside of that approved-plan flow.
- **Preserve any `@path` import lines** already present in `CLAUDE.md` (e.g. `@docs/some-file.md`) — these pull in other docs, and dropping one during a rewrite silently orphans that doc from the project's instructions. Carry them forward untouched unless the user explicitly asks to remove one.
- If `CLAUDE.md` still contains the placeholder "Awaiting project context" stub, that means intake hasn't run yet — point the user to `docs/project-intake.md` to generate the initial project-specific content instead of trying to patch the stub incrementally.
- **Notion mirror pages are never a source of truth.** If `sync-notion` reports a mismatch between a mirror page and its local file, the local file wins — don't ask the user to reconcile it, just note that the next push will correct it.

## What counts as project state

Project state means different things for a codebase-heavy project (web app, CLI, game) versus a business/service concept. Only track categories that actually apply to *this* project's type (per the classification from `docs/project-intake.md`) — don't add a section just because it's on this list.

- **Tech stack & dependencies** — language/framework versions, new or removed packages (`package.json`, `pyproject.toml`, `go.mod`, `Gemfile`, etc.)
- **Directory/architecture shape** — new top-level modules, services, or significant restructuring
- **Conventions & commands** — build/test/lint/format commands, if changed or never documented
- **External integrations** — APIs, MCP servers, databases, third-party services newly wired in
- **Environment & config** — new required env vars, config files, secrets setup
- **Deployment/CI** — new workflows, hosting changes
- **Goal/mission** — what the business or service is trying to accomplish
- **Audience** — who it's for, who the customer/user actually is
- **Owner** — who's driving this (the user's role: solo founder, side project, part of a team, etc.)
- **Competitive landscape** — existing products/companies that overlap, and how this differs (mirrors the "Existing solutions check" habit the user expects Claude to apply — don't relitigate that check, just keep CLAUDE.md's record of it current as the landscape or positioning shifts)
- **Stage/model** — how it makes money (or plans to), what stage it's at (idea, validating, building, live)
- **Key decisions/constraints** — choices made in conversation that aren't obvious from the code or from a casual read of the idea

Note what's deliberately **not** in this list: open TODOs / unfinished work. That category has a single dedicated home — `docs/backlog.md` — and does not get a CLAUDE.md section of its own. If you find yourself about to write "next steps" or "open dependencies" prose into CLAUDE.md, stop and route it to `docs/backlog.md` instead (see Companion docs below). CLAUDE.md may still contain narrative continuity about the *currently active* item (why it's blocked, scope notes, context a future session needs) — that's not a checklist, it's the story behind one line in the backlog.

When in doubt about which bucket a project falls in, or whether a hybrid applies (e.g., a business concept that's now also a real codebase), check what's already in CLAUDE.md's `## Project` section rather than guessing — it should reflect the classification intake already established.

## Noticing drift

At the Claude-solicited checkpoint, do a cheap staleness check before asking — don't just fire the generic prompt every time:

- Compare CLAUDE.md's `## Project` section against fast signals: presence/recency of manifest files, top-level directory listing, `git log --oneline -10` for recent thematic shifts.
- If a signal contradicts or is missing from CLAUDE.md relative to the categories above (e.g., CLAUDE.md says "no external services" but a new MCP server or API client just showed up), name that specific gap when soliciting the update instead of asking generically.
- Reconcile sections, not just facts: **add** a section for a category that now applies but isn't documented yet, **remove** a section whose category no longer applies (e.g., the project pivoted away from it, or it was scaffolding that never materialized), and **update** a section whose content is stale but still relevant. Propose all three kinds of changes together rather than only ever appending.
- This only changes *what you ask* — the hard rule still applies: never write without explicit go-ahead.
- **Self-report the drift check itself**, every run: the plan presented via `EnterPlanMode`/`ExitPlanMode` must include a visible log of which signals were checked (manifest/directory state, `git log` themes, CLAUDE.md's `## Project` section, and consistency against `docs/backlog.md`, `docs/activity-log.md`, and `docs/design-decisions.md`) and the finding for each — including an explicit "no drift" where nothing was found — not just the resulting proposed edits. This makes the check auditable rather than silent.

**Full drift-pass, not just session-triggered:** don't only check `docs/design-decisions.md` against *this session's* work — that misses staleness in sections nobody touched recently (e.g. a note that was never revisited after the thing it describes actually changed elsewhere). At every Claude-solicited checkpoint, do one pass over the full `docs/design-decisions.md` file, not just the parts relevant to today, and flag anything that contradicts current reality even if this session didn't cause it.

## Notion sync

If this project has a Notion workspace (check `CLAUDE.md` for a `### Notion workspace` heading), this skill's drift-check pass and its write also cover Notion, via the `sync-notion` skill:

- **At the start of this skill's drift-check pass**, run `sync-notion`'s pull step: check the `notion-to-code-sync` page for unprocessed entries and surface them to the user before proceeding with the rest of the drift check. Treat anything surfaced there the same as any other drift signal — it may change what this run's plan should include.
- **After the local write is approved and made**, run `sync-notion`'s push step to mirror the same `CLAUDE.md`/`docs/backlog.md`/`docs/activity-log.md`/`docs/design-decisions.md` changes to their Notion pages, under the same go-ahead — don't ask separately.
- If `CLAUDE.md` has no `### Notion workspace` heading, or the heading is still a placeholder (no real page URLs yet), this project hasn't been bootstrapped into Notion — skip both steps silently, don't prompt to set it up unless the user brings it up.

## Companion docs — keep docs/backlog.md and docs/activity-log.md in sync

`CLAUDE.md` is never the only doc that goes stale. Every time this skill runs, treat it as a pass over the project's whole doc set, not just `CLAUDE.md`:

- **`docs/backlog.md`** — the single flat checklist of open work; this is the only place TODO/checkbox items live, project-wide. Check off items the session's work completed, add any new items it surfaced, and re-prioritize if the session changed what's next. This replaces any checklist that used to live in `docs/activity-log.md` or in CLAUDE.md prose — do not create or maintain a second checklist anywhere else.
- **`docs/activity-log.md`** — prose only: current-state summary + a per-session changelog entry (newest-first) summarizing what happened this session. No checklist section belongs here — if you find yourself about to write `- [ ]` into this file, that line belongs in `docs/backlog.md` instead.
- **`docs/design-decisions.md`** — locked decisions only. If the session's work resolved, added, or contradicted something documented there, flag it explicitly and propose the edit — don't let CLAUDE.md silently diverge from it. If a "decision" in this file is actually still open (a flagged risk, a deferred alternative, an unresolved question), that's a backlog item wearing the wrong hat — propose moving it to `docs/backlog.md` rather than leaving it here.
- Bundle all of it — `CLAUDE.md`, `docs/backlog.md`, `docs/activity-log.md`, and `docs/design-decisions.md` if it needs a touch — into the **same** proposed plan and the same go-ahead. Don't ask for approval on `CLAUDE.md` alone and then separately edit the others unprompted afterward; the hard rule (no write without explicit go-ahead) applies to the whole batch.
