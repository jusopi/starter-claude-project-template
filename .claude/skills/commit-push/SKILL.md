---
name: commit-push
description: Commit staged/unstaged changes, push to remote, and optionally open a PR. Confirms the commit message before pushing unless --no-confirm or -y is passed. Explicit checkpoint trigger — does not auto-invoke.
agent: git-checkpoint
context: fork
disable-model-invocation: true
---

Delegate to the git-checkpoint agent: review changes, write a succinct line-item commit message, then show it and STOP — wait for the user's explicit confirmation before committing, unless `--no-confirm` or `-y` is present below, in which case commit and push immediately. Once committed and pushed, ask whether to open a PR. If yes, write a meaningful PR description and open it.

$ARGUMENTS
