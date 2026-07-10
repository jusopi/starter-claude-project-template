---
name: update-claude
description: Update CLAUDE.md with new project context or decisions. Use when the user says things like "update CLAUDE.md", "sync the CLAUDE.md", or "add that to CLAUDE.md", or at the end of a session/after a major decision to offer an update.
---

# Update CLAUDE.md

`CLAUDE.md` is a living document. There are two ways an update happens:

**User-initiated:** if the user says something like *"update CLAUDE.md"*, *"sync the CLAUDE.md"*, or *"add that to CLAUDE.md"*, treat it as a direct instruction — summarize what changed, propose the specific edit, and write it once confirmed.

**Claude-solicited:** at the end of a session, or after a major decision/milestone, ask: *"Want me to update CLAUDE.md with what we decided/built today?"* Wait for a yes before writing anything. Don't solicit more than once per session unless something changed after the first ask.

## Hard rules

- Never write to `CLAUDE.md` without an explicit go-ahead from the user first. To make that go-ahead real rather than a formality, switch to plan mode (`EnterPlanMode`) before drafting the update: present the proposed additions/removals/edits as a plan and let the normal plan-approval flow (`ExitPlanMode`) gate the actual write. Don't edit `CLAUDE.md` directly outside of that approved-plan flow.
- **Never modify `docs/working-with-user.md`** — it's fixed, applies across all projects, and is not part of this skill's scope. `CLAUDE.md` imports it via `@docs/working-with-user.md`; only edit the project-specific content in `CLAUDE.md` itself.
- **Always preserve the top-level import block** in `CLAUDE.md` (the section marked `DO NOT ERASE THIS SECTION`), including the `@docs/working-with-user.md` import line. Never erase or remove it, even when rewriting the rest of the file.
- If `CLAUDE.md` still contains the placeholder "Awaiting project context" stub, that means intake hasn't run yet — point the user to `docs/project-intake.md` to generate the initial project-specific content instead of trying to patch the stub incrementally.

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
- **Competitive landscape** — existing products/companies that overlap, and how this differs (mirrors the "Existing solutions check" habit in `docs/working-with-user.md` — don't relitigate that check, just keep CLAUDE.md's record of it current as the landscape or positioning shifts)
- **Stage/model** — how it makes money (or plans to), what stage it's at (idea, validating, building, live)
- **Key decisions/constraints** — choices made in conversation that aren't obvious from the code or from a casual read of the idea
- **Known TODOs/open issues** — worth surfacing to a future session

When in doubt about which bucket a project falls in, or whether a hybrid applies (e.g., a business concept that's now also a real codebase), check what's already in CLAUDE.md's `## Project` section rather than guessing — it should reflect the classification intake already established.

## Noticing drift

At the Claude-solicited checkpoint, do a cheap staleness check before asking — don't just fire the generic prompt every time:

- Compare CLAUDE.md's `## Project` section against fast signals: presence/recency of manifest files, top-level directory listing, `git log --oneline -10` for recent thematic shifts.
- If a signal contradicts or is missing from CLAUDE.md relative to the categories above (e.g., CLAUDE.md says "no external services" but a new MCP server or API client just showed up), name that specific gap when soliciting the update instead of asking generically.
- Reconcile sections, not just facts: **add** a section for a category that now applies but isn't documented yet, **remove** a section whose category no longer applies (e.g., the project pivoted away from it, or it was scaffolding that never materialized), and **update** a section whose content is stale but still relevant. Propose all three kinds of changes together rather than only ever appending.
- This only changes *what you ask* — the hard rule still applies: never write without explicit go-ahead.
