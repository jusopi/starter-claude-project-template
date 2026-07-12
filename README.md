# Claude Project Template

A starter template for new projects built with [Claude Code](https://claude.com/claude-code). It bootstraps a `CLAUDE.md` through a guided intake conversation and provides a skill for keeping `CLAUDE.md` up to date as the project evolves.

## Where to start

1. Get a fresh copy of this template into a new project directory, with no ties back to the template's own git repo (see below).
2. Open the project in Claude Code and run intake:
   > "Run project intake" (or point Claude at `docs/project-intake.md`)

   Claude will ask what type of project this is (game, web app, backend/API, CLI tool, data pipeline, business/service concept, etc.), ask targeted follow-up questions for that type, ask about tooling/workflow (project tracking, ideation tools, MCP servers, deploy/stack defaults), confirm it has what it needs, then write a project-specific `CLAUDE.md`.
3. From then on, `CLAUDE.md` is your project's live source of truth. Keep working with Claude as normal — use `/update-claude` (see below) whenever the project's context, decisions, or conventions change.

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

- **`CLAUDE.md`** — the project's root instruction file, loaded automatically by Claude Code. Starts as a stub with a placeholder `## Project` section. Intake replaces the stub's project section.
- **`docs/project-intake.md`** — the guided intake script Claude follows to classify the project, ask the right questions for that type, and generate the initial `CLAUDE.md`. Stays in the repo permanently in case you need to re-run intake later (e.g. project scope changes significantly).
- **`.claude/skills/update-claude/SKILL.md`** — the `/update-claude` skill, described below.

## Features

**Guided intake** — `docs/project-intake.md` adapts its questions to your project type instead of using one generic checklist, and always checks in before writing anything to `CLAUDE.md`.

**`/update-claude` skill** — invoke it any time you want `CLAUDE.md` updated (e.g. "update CLAUDE.md with what we just decided"), or let Claude offer to update it at the end of a session or after a major decision. It always confirms with you before writing.

## Conventions to keep in mind

- Treat `CLAUDE.md` as living documentation: update it through `/update-claude` rather than editing it silently, so changes are deliberate and confirmed.
