---
name: update-claude
description: Update CLAUDE.md with new project context or decisions. Use when the user says things like "update CLAUDE.md", "sync the CLAUDE.md", or "add that to CLAUDE.md", or at the end of a session/after a major decision to offer an update.
---

# Update CLAUDE.md

`CLAUDE.md` is a living document. There are two ways an update happens:

**User-initiated:** if the user says something like *"update CLAUDE.md"*, *"sync the CLAUDE.md"*, or *"add that to CLAUDE.md"*, treat it as a direct instruction — summarize what changed, propose the specific edit, and write it once confirmed.

**Claude-solicited:** at the end of a session, or after a major decision/milestone, ask: *"Want me to update CLAUDE.md with what we decided/built today?"* Wait for a yes before writing anything. Don't solicit more than once per session unless something changed after the first ask.

## Hard rules

- Never write to `CLAUDE.md` without an explicit go-ahead from the user first.
- **Never modify `docs/working-with-user.md`** — it's fixed, applies across all projects, and is not part of this skill's scope. `CLAUDE.md` imports it via `@docs/working-with-user.md`; only edit the project-specific content in `CLAUDE.md` itself.
- **Always preserve the top-level import block** in `CLAUDE.md` (the section marked `DO NOT ERASE THIS SECTION`), including the `@docs/working-with-user.md` import line. Never erase or remove it, even when rewriting the rest of the file.
- If `CLAUDE.md` still contains the placeholder "Awaiting project context" stub, that means intake hasn't run yet — point the user to `docs/project-intake.md` to generate the initial project-specific content instead of trying to patch the stub incrementally.
