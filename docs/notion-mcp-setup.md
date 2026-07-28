# Notion MCP Setup

This is a **one-time, per-machine, manual step** — it can't be done by
Claude Code editing files, since connecting a hosted MCP server requires
an interactive OAuth login in a browser. Run this once before `sync-notion`
can do anything.

## 1. Add the server

From the repo root:

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp --scope project
```

`--scope project` writes the server into `.mcp.json` at the repo root,
which is meant to be checked into version control — so everyone working
on this project gets the same declared connector, without each person
needing to remember this command.

## 2. Authenticate

Project-scoped servers from a checked-in `.mcp.json` start out pending
approval. Inside a Claude Code session, run:

```
claude
```

and accept the workspace-trust dialog if prompted. Then run:

```
/mcp
```

and follow the browser login flow to authorize Claude Code's access to
your Notion workspace. Once connected, `notion` should show as `✔ Connected`
in `/mcp` or `claude mcp list`.

## 3. Verify

```bash
claude mcp get notion
```

should report a connected status. If it doesn't, re-run `/mcp` and
check that the OAuth flow actually completed in the browser — a closed
tab or a cancelled login leaves the server showing `! Needs authentication`.

## Notes

- Each teammate/clone needs to run step 2 on their own machine — the
  `.mcp.json` declaration is shared, but the OAuth token is not.
- If this project hasn't been bootstrapped into Notion yet (no
  `### Notion workspace` heading in `CLAUDE.md`), run `bootstrap-notion-project`
  first — there's nothing for `sync-notion` to push to or pull from until
  that workspace exists.
- To remove the connector later: `claude mcp remove notion`.
