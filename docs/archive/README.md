# Archive

`backlog.md`, `activity-log.md`, and `design-decisions.md` in this folder are **one-way, read-only snapshots** pulled from the project's live Notion pages by the `/update-claude` skill. They are a git-history / GitHub-hosted backup in case Notion is ever unavailable — not a working document.

Rules:

- Never edit these files directly. Any hand edit is silently overwritten the next time `/update-claude` runs.
- Code never reads these files as a source during normal work — the live Notion pages are the source of truth. These exist purely as a fallback snapshot.
- `activity-log.md` is refreshed on every `/update-claude` run, unconditionally.
- `backlog.md` and `design-decisions.md` are refreshed together, only when a session carries an explicit "this solidified" / "lock this in" signal — they're paired because a locked decision and the backlog item it resolves should never drift apart in the snapshot.
- Nothing here is ever pushed back to Notion. The archive is a one-way mirror, Notion → repo, never the reverse.
