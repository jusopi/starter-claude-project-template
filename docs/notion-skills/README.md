# Notion account-level skills

`bootstrap-notion-project` and `update-notion-project` are Claude.ai
account-level skills (Settings → Skills), not project files — Claude.ai
doesn't let a repo or MCP server install them automatically. This folder is
just a drop point so a new clone of this template ships with the current
`.zip` exports alongside the setup instructions in the main `README.md`.

## Getting the current zips

1. In the Claude.ai account that already has these skills configured, go to
   Settings → Skills.
2. Download `bootstrap-notion-project` and `update-notion-project` as `.zip`
   files.
3. Place them here as `bootstrap-notion-project.zip` and
   `update-notion-project.zip`.

## Installing them for a new user/account

1. In the target Claude.ai account, go to Settings → Skills.
2. Upload each `.zip` from this folder.
3. Confirm both show up as installed before relying on the
   `sync-notion:managed` marker (see `docs/notion-mcp-setup.md` and
   `.claude/skills/sync-notion/SKILL.md`).

## Keeping them current

This is a **manual, one-way export** — Claude.ai's Settings page is the live
source of truth for these two skills, not this folder. If either skill is
edited in Claude.ai after the zips here were exported, the copies in this
repo go stale silently; nothing checks or warns about drift. Re-export and
replace the file here whenever the skill changes, the same way you'd update
a vendored dependency.
