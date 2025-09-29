# Opencode Hooks

This file defines pre and post hooks for opencode workflows, following React-inspired patterns for modular, reusable logic. Hooks are mandatory for plan execution to ensure consistent git practices per gitrules.md.

## usePreHook: Branch Creation

**Purpose:** Isolate new plans by creating a dedicated branch before any changes, preventing direct commits to main.

**Process:**
1. Verify current branch is 'main'; abort if not.
2. Prompt user for branch type (feature, bugfix, docs, refactor) and short description.
3. Generate branch name per convention: `<type>/<description>` (e.g., `feature/add-user-auth`).
4. Run `git checkout -b <generated-name>`.
5. Log success and confirm branch switch.

**Example Script (Bash):**
```bash
#!/bin/bash
if [ "$(git branch --show-current)" != "main" ]; then
  echo "Error: Must be on main branch to create new branch."
  exit 1
fi
read -p "Branch type (feature/bugfix/docs/refactor): " type
read -p "Description: " desc
branch_name="${type}/${desc// /-}"
git checkout -b "$branch_name"
echo "Created and switched to branch: $branch_name"
```

**Usage:** Run this hook at the start of every new plan.

## usePostHook: Rebase PR Merging and Cleanup

**Purpose:** Finalize plans by pushing changes, creating a PR, merging with rebase for linear history, and cleaning up branches.

**Process:**
1. Push current branch to remote: `git push -u origin <branch>`.
2. Create PR with analysis: Use `gh pr create` with title, body summarizing changes (per gitrules.md PR guidelines).
3. Merge PR with rebase: Use `gh pr merge <number> --rebase`.
4. Switch to main and pull updates: `git checkout main && git pull origin main`.
5. Delete branches: `git branch -d <branch>` locally and `git push origin --delete <branch>` remotely.

**Example Script (Bash):**
```bash
#!/bin/bash
branch=$(git branch --show-current)
if [ "$branch" = "main" ]; then
  echo "Error: Cannot run post-hook on main branch."
  exit 1
fi
git push -u origin "$branch"
# Assume PR creation is manual or integrated; for automation, add gh pr create here
# After PR merge (manual step), run:
git checkout main
git pull origin main
git branch -d "$branch"
git push origin --delete "$branch"
echo "Post-hook completed: Merged and cleaned up $branch"
```

**Usage:** Run this hook after committing changes in a plan.

## Integration Notes
- Reference this file in AGENTS.md as @hooks.md for lazy loading.
- Hooks ensure compliance with gitrules.md without bloating AGENTS.md.
- For automation, integrate scripts into opencode workflows.