# Project Intake

You are running project intake for a newly cloned template repo. Your job is to gather enough context to write a complete, project-specific `CLAUDE.md`, then replace this stub with that file.

## Step 1 — Classify

Ask the user: **What type of project is this?**

Examples: game, web app, backend/API service, CLI tool, data pipeline, automation script, business/service concept, or something else.

Wait for their answer before proceeding. Do not guess.

## Step 2 — Ask targeted questions

Every branch below is a **starting point, not a script**. Ask the seed questions for the matching type, then reason live from the answers: think briefly about what you still need to know, and ask follow-ups that fit what this specific person is actually describing. Don't just march down a checklist once seed questions are exhausted — treat every branch with the same live-reasoning treatment as Business/Service below.

### Game — seed questions

- What's the core gameplay loop or central mechanic?
- Genre and tone/theme?
- Target platform(s)? (desktop, mobile, web, console)
- Engine? (Godot, Unity, custom, other)
- Art style and scope — placeholder art, full pipeline, or asset packs?
- Solo or team? Any collaborators with defined roles?
- Rough scope: prototype, vertical slice, or full release?

Then reason from the answers — e.g., a multiplayer answer should trigger follow-ups about netcode and server architecture that aren't in the seed list.

### Web App — seed questions

- What core problem does this solve, and for whom?
- Frontend stack preference (or no preference)?
- Backend/language preference?
- Does it need user auth? Database? Third-party integrations (payments, email, etc.)?
- Deployment target (Vercel, AWS, self-hosted, undecided)?
- Rough timeline or urgency?

Then reason from the answers — e.g., mention of payments should trigger a follow-up about compliance/PCI scope that isn't in the seed list.

### Business / Service concept — no seed list

Reason through it live from the start:

1. Think out loud, briefly, about what you'd need to know to help this person build a `CLAUDE.md` for a business/service project — e.g., what problem they're solving, who the customer is, how it makes money, what stage they're at.
2. Ask 4-6 questions based on that reasoning, tailored to what they've told you so far.
3. If an answer reveals this is actually closer to a web app or game (e.g., "it's a subscription app"), pivot to blend in relevant seed questions from those branches.

### Anything else (CLI tool, data pipeline, automation, etc.)

Same live-reasoning approach as Business/Service: reason briefly about what matters for that specific project type, then ask 4-6 targeted questions. Do not force-fit into the Game or Web App sets.

## Step 3 — Tooling & workflow (applies to every branch)

Regardless of project type, ask about the following. Offer the suggested defaults below as options rather than asking cold — it's faster for the user to confirm/reject a suggestion than to generate one from scratch.

**Project tracking:** Ask if they want a system in place. Suggest based on scope: Linear for solo/small dev-native tracking, GitHub Projects if everything's already in GitHub and they want zero extra tools, Jira only if there's a team with formal sprints, generic kanban (Trello) for casual tracking with no dev integration needs.

**Ideation tools:** Ask what they're already using or want to use for early reasoning/design — e.g. Claude.ai chats for spec drafting, Figma/FigJam or Miro for visual or collaborative ideation, Grok/Copilot as secondary sounding boards. Don't assume; just confirm what's already part of their workflow.

**MCP (Model Context Protocol):** Ask if they plan to use MCP servers for this project — e.g. connecting to GitHub, databases, Figma, or other services directly from Claude. If yes, ask which integrations matter so `CLAUDE.md` can note which MCP connectors this project expects to have available.

**Project-type-specific tooling:** Offer relevant defaults for the classified type rather than asking generically:
- *Game:* Godot or Unity for engine, Aseprite for pixel art, itch.io for playtest distribution.
- *Web app:* Vercel for deploy, Supabase for managed backend/auth/db, v0.dev for fast UI scaffolding.
- *Business/service:* Notion for docs/ops, Stripe for payments, Metabase for lightweight analytics.
- *Other types:* reason live about what's standard for that domain, same as the adaptive question-branches above.

For each category, capture whatever the user actually wants (confirmed default, a different tool, or none) — don't assume a tool's conventions without confirming them, and don't force a tool on them if they say no.

If a spec/PM system is planned, ask a quick follow-up on how it should integrate — e.g., should Claude read/write spec files directly, reference issue IDs in commits, check a backlog file before starting work. Note in `CLAUDE.md` if no formal system is in use, so future sessions don't go looking for one.

## Step 4 — Check before finalizing

After your first round of questions, ask the user directly: **"Does this cover what matters, or is there something I should dig into differently?"**

Give them a chance to redirect before you write anything. Don't skip this step.

## Step 5 — Generate CLAUDE.md

Once you have enough context:

1. Synthesize all answers into a complete `CLAUDE.md` covering: project overview, tech/tooling decisions, constraints, conventions, and anything Claude should know to work autonomously on this project going forward.
2. Replace the stub `CLAUDE.md` in this repo with the generated version.
3. Leave `docs/project-intake.md` in place — do not delete it. It stays in the repo in case the project needs re-intake or scope revisiting later.
4. **Never modify `docs/working-with-user.md`** — it's fixed, applies across all projects, and is not part of intake output. `CLAUDE.md` imports it via `@docs/working-with-user.md`; only replace the project-specific content in `CLAUDE.md` itself.

## Ongoing maintenance

CLAUDE.md is a living document. Ongoing updates (user-initiated or Claude-solicited) are handled by the `/update-claude` skill — see `.claude/skills/update-claude/SKILL.md`.
