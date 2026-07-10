# Claude Project Template

A starter template for new projects built with [Claude Code](https://claude.com/claude-code). It bootstraps a `CLAUDE.md` through a guided intake conversation, keeps a fixed set of collaboration preferences separate from project-specific content, and provides a skill for keeping `CLAUDE.md` up to date as the project evolves.

## Where to start

1. Clone this template into a new project directory.
2. Open the project in Claude Code and run intake:
   > "Run project intake" (or point Claude at `docs/project-intake.md`)

   Claude will ask what type of project this is (game, web app, backend/API, CLI tool, data pipeline, business/service concept, etc.), ask targeted follow-up questions for that type, ask about tooling/workflow (project tracking, ideation tools, MCP servers, deploy/stack defaults), confirm it has what it needs, then write a project-specific `CLAUDE.md`.
3. From then on, `CLAUDE.md` is your project's live source of truth. Keep working with Claude as normal — use `/update-claude` (see below) whenever the project's context, decisions, or conventions change.

## How it's structured

- **`CLAUDE.md`** — the project's root instruction file, loaded automatically by Claude Code. Starts as a stub with a placeholder `## Project` section and two required `@path` imports. Intake replaces the stub's project section; the import lines must never be removed.
- **`docs/working-with-user.md`** — fixed, imported into every project via `@docs/working-with-user.md`. Defines how Claude should communicate and collaborate (when to ask clarifying questions vs. proceed on assumptions, direct/concise communication style, checking for existing solutions before building new ones, output formatting rules). This file is never modified by intake or by ongoing maintenance — it applies the same way across every project cloned from this template.
- **`docs/project-intake.md`** — the guided intake script Claude follows to classify the project, ask the right questions for that type, and generate the initial `CLAUDE.md`. Stays in the repo permanently in case you need to re-run intake later (e.g. project scope changes significantly).
- **`.claude/skills/update-claude/SKILL.md`** — the `/update-claude` skill, described below.

## Features

**Guided intake** — `docs/project-intake.md` adapts its questions to your project type instead of using one generic checklist, and always checks in before writing anything to `CLAUDE.md`.

**Fixed collaboration preferences** — `docs/working-with-user.md` travels unchanged with every project cloned from this template, so Claude's communication style and decision-making defaults stay consistent across all your projects while `CLAUDE.md` itself stays project-specific.

**`/update-claude` skill** — invoke it any time you want `CLAUDE.md` updated (e.g. "update CLAUDE.md with what we just decided"), or let Claude offer to update it at the end of a session or after a major decision. It always confirms with you before writing, and it will never touch `docs/working-with-user.md` or remove the required `@path` import lines at the top of `CLAUDE.md`.

## Conventions to keep in mind

- `docs/working-with-user.md` is fixed from Claude's perspective — intake and `/update-claude` never touch it. You can still hand-edit it yourself; just do so deliberately and know the scope: editing it inside an already-cloned project only affects that project, but editing it in this template repo changes what every future clone starts with.
- Never remove the `@docs/working-with-user.md` import line at the top of `CLAUDE.md` — everything under the "DO NOT ERASE THIS SECTION" header must stay intact.
- Treat `CLAUDE.md` as living documentation: update it through `/update-claude` rather than editing it silently, so changes are deliberate and confirmed.
