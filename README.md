# Claude Project Template

A starter template for new projects built with [Claude Code](https://claude.com/claude-code). It bootstraps a `CLAUDE.md` through a guided intake conversation, provides a skill for keeping `CLAUDE.md` up to date as the project evolves, and (optionally) pairs the repo with a Claude.ai project via a shared Notion **Project Links** database.

## Machine-level prerequisites

Some of what this template assumes lives **outside** this repo, on the machine running Claude Code — not per-project, per-machine:

- `~/.claude/CLAUDE.md` — your personal global instructions.
- `~/.claude/skills/link-project/` — a personal skill that looks up/creates/updates rows in the Notion Project Links database (see below). Intake and `/update-claude` both invoke it rather than reimplementing that logic.

Neither of these is provisioned by this repo, and this repo does not and should not write to `~/.claude` automatically. A separate, one-time prompt/script (outside this template) sets them up on a new machine; if `link-project` isn't installed, intake and `/update-claude` will say so rather than silently skipping the Project Links step.

## Where to start

Two equally valid entry points, depending on which side comes first:

**Code-first** — you're starting from the repo:
1. Get a fresh copy of this template into a new project directory, with no ties back to the template's own git repo (see "Decoupling from the template repo" below).
2. Open the project in Claude Code and run intake:
   > "Run project intake" (or point Claude at `docs/project-intake.md`)

   Claude will check the Project Links database for an existing row first (in case an ai Project already exists for this idea), ask what type of project this is (game, web app, backend/API, CLI tool, data pipeline, business/service concept, etc.), ask targeted follow-up questions for that type, ask about tooling/workflow (project tracking, ideation tools, MCP servers, Notion project tracking), confirm it has what it needs, then write a project-specific `CLAUDE.md` and create/update the Project Links row.
3. From then on, `CLAUDE.md` is your project's live source of truth for code-side context. Keep working with Claude as normal — use `/update-claude` (see below) whenever the project's context, decisions, or conventions change.

**ai-first** — you're starting from a Claude.ai voice/chat session, no code repo yet:
1. Talk through the idea in Claude.ai. Once it has a name and a real shape, `bootstrap-notion-project` (see "Project Links database" below) sets up its Notion workspace and creates an `ai-only` Project Links row.
2. When you're ready to start building, clone this template (per "Decoupling" below) and run intake as in the code-first path — Step 0 of intake finds the existing `ai-only` row, pulls in what's already been discussed, and flips it to `linked` once the repo's URL is known.

Neither path requires the other to happen first; the Project Links row is what lets whichever side comes second find and reconnect to whichever side came first.

## Decoupling from the template repo

A plain `git clone` leaves `origin` pointed at this template repo, so pushing would try to push into `starter-claude-project-template` itself. Pick one:

**Option A — `degit` (recommended):** copies the files with no git history at all, so there's nothing to decouple.

```
npx degit jusopi/starter-claude-project-template my-new-project
cd my-new-project
git init
```

**Option B — manual clone:** clone normally, then strip the template's history and start clean.

```
git clone https://github.com/jusopi/starter-claude-project-template.git my-new-project
cd my-new-project
rm -rf .git
git init
```

Either way, once you've set up your own remote (`git remote add origin <your-new-repo-url>`), you're fully independent of the template — future changes to the template repo won't affect your project, and vice versa.

## How it's structured

- **`CLAUDE.md`** — the project's root instruction file, loaded automatically by Claude Code. Starts as a stub with a placeholder `## Project` section. Intake replaces the stub's project section. Covers project identity, stack, and conventions — not a duplicate of Backlog/Activity Log/Design Decisions/Open Discussions, which live in Notion (see below).
- **`docs/project-intake.md`** — the guided intake script Claude follows to classify the project, ask the right questions for that type, generate the initial `CLAUDE.md`, and set up the Project Links row. Available on demand ("run project intake") or auto-offered when Code finds no populated `CLAUDE.md` and no matching Project Links row at all. Stays in the repo permanently in case you need to re-run intake later.
- **`docs/archive/`** — read-only, one-way local snapshots of the project's Notion Backlog/Activity Log/Design Decisions/Open Discussions pages, refreshed by `/update-claude`. A GitHub-hosted backup in case Notion is ever unavailable, never a working document — see `docs/archive/README.md`.
- **`docs/notion-mcp-setup.md`** — one-time, per-machine Notion MCP OAuth setup, needed for Project Links lookups and any direct Notion reads/writes during a session.
- **`docs/notion-skills/`** — backup exports of the account-level Claude.ai skills (`bootstrap-notion-project`, `update-notion-project`) that manage a project's Notion workspace from the ai side.
- **`.claude/skills/update-claude/SKILL.md`** — the `/update-claude` skill, described below.

## Features

**Guided intake** — `docs/project-intake.md` adapts its questions to your project type instead of using one generic checklist, checks the Project Links database before assuming a project is brand new, and always checks in before writing anything to `CLAUDE.md`.

**`/update-claude` skill** — invoke it any time you want `CLAUDE.md` updated (e.g. "update CLAUDE.md with what we just decided"), or let Claude offer to update it at the end of a session or after a major decision. It always confirms with you before writing to `CLAUDE.md`. On every run it also refreshes `docs/archive/activity-log.md` and `docs/archive/open-discussions.md` from Notion and bumps the Project Links row's Last Synced date, unconditionally; if the session carries an explicit "this solidified"/"lock this in" signal, it additionally archives Backlog and Design Decisions together. It never pushes local content back to Notion — Notion is the only writable copy of those three pages.

## Project Links database (Notion)

**Project Links** is a Notion database, one row per project, that pairs a Claude.ai project ("ai side") with a code repo ("code side"): Project Name (the join key), AI Project URL, Code Repo URL, Link Status (`ai-only` / `code-only` / `linked` / `stale`), Last Synced, plus relations to that project's Backlog / Activity Log / Design Decisions / Open Discussions pages.

This database already exists in your Notion workspace (created once per user, not per project) — this repo doesn't provision it, only reads and writes rows in it via the `link-project` personal skill (see "Machine-level prerequisites" above). Intake looks up this project's row before deciding whether to create one; `bootstrap-notion-project` (the ai-side skill, see `docs/notion-skills/`) does the same when a project solidifies from the Claude.ai side.

Backlog, Activity Log, Design Decisions, and Open Discussions are **Notion pages**, not local files — read and written live via Notion MCP during a session, and treated as the sole source of truth. `docs/archive/*.md` is a periodic read-only snapshot for backup, never a working copy Code reads from during normal work.

**Open Discussions** is the fourth of these Notion pages: ongoing, inconclusive, topic-based ideas picked up across multiple sessions that have no defined next action and no resolution yet. An item lives there only while genuinely unresolved — it graduates out exactly once, into Backlog (once it becomes a concrete actionable item) or into Design Decisions (once it's settled), and is removed from Open Discussions at that point; it's never left duplicated in both places after graduating. Unlike Backlog/Design Decisions, its archive snapshot (`docs/archive/open-discussions.md`) refreshes every `/update-claude` run unconditionally, same cadence as Activity Log, since it has no locked/settled state to gate a refresh on.

The paired Claude.ai side's workspace also has an **Instructions** page, but only for projects still `ai-only` — once a code repo exists (`linked`), `CLAUDE.md` is that project's standing-context doc and no Instructions page is created or maintained going forward.

Prerequisite: `docs/notion-mcp-setup.md`'s one-time, per-machine Notion MCP OAuth connection.

## Conventions to keep in mind

- Treat `CLAUDE.md` as living documentation: update it through `/update-claude` rather than editing it silently, so changes are deliberate and confirmed.
- Treat Notion's Backlog/Activity Log/Design Decisions/Open Discussions pages the same way — direct edits during a session are fine, but never hand-edit `docs/archive/*.md`; it's overwritten on the next `/update-claude` run.

## Notes

This template previously mirrored `CLAUDE.md`/`docs/backlog.md`/`docs/activity-log.md`/`docs/design-decisions.md` to Notion via a `sync-notion` skill and a `notion-to-code-sync` queue page, with local files as the master copy. That's been dissolved: with Notion pages as the only writable copy and Code reading them directly, there's no second copy left to reconcile, and no queue needed to relay ideas back into a repo that never held its own copy in the first place.
