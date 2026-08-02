---
name: update-claude
description: Update CLAUDE.md with new project context or decisions, refresh the local docs/archive/ snapshots from this project's Notion pages, and keep its Project Links row current. Use when the user says things like "update CLAUDE.md", "sync the CLAUDE.md", or "add that to CLAUDE.md", or at the end of a session/after a major decision to offer an update.
model: claude-haiku-4-5-20251001
---

# Update CLAUDE.md

`CLAUDE.md` is a living document. There are two ways an update happens:

**User-initiated:** if the user says something like *"update CLAUDE.md"*, *"sync the CLAUDE.md"*, or *"add that to CLAUDE.md"*, treat it as a direct instruction — summarize what changed, propose the specific edit, and write it once confirmed.

**Claude-solicited:** at the end of a session, or after a major decision/milestone, ask: *"Want me to update CLAUDE.md with what we decided/built today?"* Wait for a yes before writing anything. Don't solicit more than once per session unless something changed after the first ask.

Every invocation of this skill also does two things that are **not** gated behind that go-ahead, described in full below: refreshing `docs/archive/activity-log.md` from Notion, and bumping this project's Project Links row. Those are mechanical reads/snapshots, not editorial decisions about `CLAUDE.md` content — keep them separate from the write-gated flow.

## Hard rules

- **Never write to `CLAUDE.md` without an explicit go-ahead from the user first.** To make that go-ahead real rather than a formality, switch to plan mode (`EnterPlanMode`) before drafting the update: present the proposed additions/removals/edits as a plan and let the normal plan-approval flow (`ExitPlanMode`) gate the actual write. Don't edit `CLAUDE.md` directly outside of that approved-plan flow.
- **Preserve any `@path` import lines** already present in `CLAUDE.md` (e.g. `@docs/some-file.md`) — these pull in other docs, and dropping one during a rewrite silently orphans that doc from the project's instructions. Carry them forward untouched unless the user explicitly asks to remove one.
- If `CLAUDE.md` still contains the placeholder "Awaiting project context" stub, that means intake hasn't run yet — point the user to `docs/project-intake.md` to generate the initial project-specific content instead of trying to patch the stub incrementally.
- **This skill only ever reads from Notion and writes to the local `docs/archive/` snapshot — never the reverse.** There is no push flow. If a Notion page and the local archive disagree, Notion wins, silently; the next run of this skill corrects the archive.
- `CLAUDE.md` should not duplicate Backlog/Activity Log content. Track project identity, stack, conventions, and constraints here; point to Notion (link the pages, if `CLAUDE.md`'s `### Notion workspace` heading has real URLs) for Backlog/Activity Log/Design Decisions/Open Discussions rather than restating them.

## What counts as project state (for the CLAUDE.md write)

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

Note what's deliberately **not** in this list: open TODOs / unfinished work, locked design decisions, and session-by-session narrative. Those live entirely in Notion now (Backlog, Design Decisions, Activity Log respectively) — `CLAUDE.md` may still carry narrative continuity about the *currently active* item (why it's blocked, scope notes, context a future session needs), but that's not a checklist or a changelog, it's the story behind one line in Notion's Backlog.

When in doubt about which bucket a project falls in, or whether a hybrid applies (e.g., a business concept that's now also a real codebase), check what's already in CLAUDE.md's `## Project` section rather than guessing — it should reflect the classification intake already established.

## Noticing drift (for the CLAUDE.md write)

At the Claude-solicited checkpoint, do a cheap staleness check before asking — don't just fire the generic prompt every time:

- Compare CLAUDE.md's `## Project` section against fast signals: presence/recency of manifest files, top-level directory listing, `git log --oneline -10` for recent thematic shifts.
- If a signal contradicts or is missing from CLAUDE.md relative to the categories above (e.g., CLAUDE.md says "no external services" but a new MCP server or API client just showed up), name that specific gap when soliciting the update instead of asking generically.
- Reconcile sections, not just facts: **add** a section for a category that now applies but isn't documented yet, **remove** a section whose category no longer applies (e.g., the project pivoted away from it, or it was scaffolding that never materialized), and **update** a section whose content is stale but still relevant. Propose all three kinds of changes together rather than only ever appending.
- This only changes *what you ask* — the hard rule still applies: never write without explicit go-ahead.
- **Self-report the drift check itself**, every run that proposes a CLAUDE.md edit: the plan presented via `EnterPlanMode`/`ExitPlanMode` must include a visible log of which signals were checked and the finding for each — including an explicit "no drift" where nothing was found — not just the resulting proposed edits.

## Archiving from Notion (every run, unconditional)

If `CLAUDE.md` has a `### Notion workspace` heading with real page links (not the "not yet bootstrapped" placeholder), every run of this skill:

1. Fetches the project's Activity Log page via Notion MCP and writes its content verbatim to `docs/archive/activity-log.md`.
2. Fetches the project's Open Discussions page via Notion MCP and writes its content verbatim to `docs/archive/open-discussions.md`.
3. Updates this project's Project Links row via the `link-project` skill (`~/.claude/skills/link-project/SKILL.md`) — bump `Last Synced` to today.

None of these steps need a separate go-ahead — they're mechanical snapshots of what's already live in Notion, not new content being authored. Do them regardless of whether this run also produces a `CLAUDE.md` edit.

If the `### Notion workspace` heading is missing or still a placeholder, this project hasn't been bootstrapped into Notion — skip both steps silently, don't prompt to set it up unless the user brings it up.

## Solidify signal — archive Backlog + Design Decisions together

If (and only if) this invocation carries an explicit "this solidified" / "lock this in" / equivalent signal from the user, also:

1. Fetch the project's Backlog page and write it verbatim to `docs/archive/backlog.md`.
2. Fetch the project's Design Decisions page and write it verbatim to `docs/archive/design-decisions.md`.

Always do these **as a pair** — never pull one without the other, so a locked decision and the backlog item it resolves can't drift apart in the snapshot. This is additional to the unconditional Activity Log archive above, not a replacement for it.

This pair is intentionally Backlog + Design Decisions only — Open Discussions is archived unconditionally every run (see above), not gated behind the solidify signal, since it has no locked/settled state to pair against.

This pair-archive also doesn't need a separate go-ahead, for the same reason as the Activity Log archive — it's a snapshot of Notion content that already exists, not new authored content.
