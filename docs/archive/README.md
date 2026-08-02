# Archive

`backlog.md`, `activity-log.md`, `design-decisions.md`, and `open-discussions.md` in this folder are **one-way, read-only snapshots** pulled from the project's live Notion pages by the `/update-claude` skill. They are a git-history / GitHub-hosted backup in case Notion is ever unavailable — not a working document.

Rules:

- Never edit these files directly. Any hand edit is silently overwritten the next time `/update-claude` runs.
- Code never reads these files as a source during normal work — the live Notion pages are the source of truth. These exist purely as a fallback snapshot.
- `activity-log.md` and `open-discussions.md` are each refreshed on every `/update-claude` run, unconditionally.
- `backlog.md` and `design-decisions.md` are refreshed together, only when a session carries an explicit "this solidified" / "lock this in" signal — they're paired because a locked decision and the backlog item it resolves should never drift apart in the snapshot.
- Open Discussions items are ongoing, inconclusive, topic-based ideas with no defined next action and no resolution yet. An item graduates *out* of Open Discussions exactly once it either becomes concrete (moves to Backlog) or gets settled (moves to Design Decisions) — at that point it's removed from Open Discussions and never left duplicated in both places. This snapshot file just mirrors whatever the live Open Discussions page currently holds; it doesn't enforce the graduation rule itself (that's the ai-side `update-notion-project` skill's job — see `docs/notion-skills/`).
- Nothing here is ever pushed back to Notion. The archive is a one-way mirror, Notion → repo, never the reverse.
