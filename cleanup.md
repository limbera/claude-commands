---
description: Clean up a merged worktree — save .env, remove worktree + branch, pull latest main
allowed-tools: Bash, Read, Glob
---

Clean up the current git worktree after its branch has been merged. Run all steps sequentially. Abort and inform the user if any safety check fails.

## Step 1 — Safety checks

- Confirm we are inside a **worktree** (not the main working tree). Abort if not.
- Confirm there are **no uncommitted changes** (staged or unstaged). Abort if dirty.
- Confirm there are **no unpushed commits** vs the remote tracking branch. Abort if ahead.

## Step 2 — Identify paths

- Determine the **root repo path** from `git worktree list` (the first entry — the main working tree).
- Determine the **current worktree path** and **branch name**.
- Print all three for the user to confirm before proceeding.

## Step 3 — Preserve `.env`

- If a `.env` file exists in the current worktree, copy it to the root repo path (overwrite only if the worktree copy is newer or the root copy doesn't exist).

## Step 4 — Remove worktree and branch

- `cd` to the root repo path first (you can't remove a worktree while inside it).
- Run `git worktree remove <worktree-path>`.
- Delete the local branch: `git branch -d <branch-name>`.
- Delete the remote branch if it still exists: `git push origin --delete <branch-name>` (skip gracefully if already deleted).

## Step 5 — Pull latest main

- From the root repo, run `git pull --ff-only` to update main.
- If fast-forward fails, warn the user instead of forcing.

## Step 6 — Done

- Print a summary of what was cleaned up.
- Tell the user to run `/exit` to close this Claude session (since the worktree no longer exists).
