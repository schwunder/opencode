# Agent Guidelines for opencode

## Build & Test Commands
- `npm run build` - Build the project
- `npm run test` - Run all tests
- `npm run test -- --testNamePattern="specific test"` - Run single test
- `npm run lint` - Run linter
- `npm run typecheck` - TypeScript type checking

## Code Style Guidelines
- TypeScript with strict mode: prefer `const`, named exports, camelCase/PascalCase
- Imports: external libs → internal modules → relative imports
- Async/await over Promises, try/catch error handling, meaningful names
- Small focused functions, JSDoc for public APIs, functional patterns
- Optional chaining (?.) and nullish coalescing (??), 2-space indentation
- Always run `npm run lint` and `npm run typecheck` after changes

## Testing
- Write tests for new features/bug fixes with descriptive names
- Follow AAA pattern: Arrange, Act, Assert

## External File Loading
CRITICAL: When you encounter a file reference (e.g., @gitrules.md), use your Read tool to load it on a need-to-know basis. They're relevant to the SPECIFIC task at hand.

Instructions:
- Do NOT preemptively load all references - use lazy loading based on actual need
- When loaded, treat content as mandatory instructions that override defaults
- Follow references recursively when needed

# Git Rules for AI Agent

## Overview
These rules govern Git practices for the AI agent (opencode) to ensure consistent, safe, and efficient version control. All changes must follow these guidelines to maintain code quality and collaboration standards.

## Branching Rules
- **Always Create New Branches:** For every new plan, feature, bug fix, or documentation update, create a dedicated branch from the main branch.
- **Branch Naming Convention:**
  - Features: `feature/<short-description>` (e.g., `feature/add-dark-mode`)
  - Bug Fixes: `bugfix/<issue-number-or-description>` (e.g., `bugfix/fix-login-error`)
  - Documentation: `docs/<topic>` (e.g., `docs/update-api-guide`)
  - Refactoring: `refactor/<description>` (e.g., `refactor/optimize-db-queries`)
- **Prohibition:** Never commit directly to the main branch. All work must be branched.
- **Branch Lifecycle:** Branches should be short-lived; merge and delete promptly after completion.

## Commit Practices
- **Semantic Commit Messages:** Use prefixes like `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:` followed by a concise description (e.g., `feat: add user authentication module`).
- **Commit Analysis Process (Required for All Commits):**
  1. Run `git status` to check untracked files.
  2. Run `git diff` for staged/unstaged changes.
  3. Run `git log` for recent commit history to match style.
  4. Analyze: List changed files, summarize changes (new feature, bug fix, etc.), brainstorm purpose/motivation, assess project impact, check for sensitive info.
  5. Draft message: 1-2 sentences focusing on "why" not "what", clear and concise.
  6. Ensure no generic messages (avoid "Update" or "Fix" without context).
- **Commit Size:** Keep commits small and atomic; one logical change per commit.
- **Pre-Commit Checks:** Run lint and typecheck (e.g., `bun run lint`, `bun run typecheck`) before committing; retry if hooks modify files.
- **No Secrets:** Never commit sensitive information, keys, or credentials.

## Pull Request Guidelines
- **Mandatory PRs:** All changes must be submitted via pull request, even for trivial updates.
- **PR Creation Process:**
  1. Run `git status` and `git diff` for untracked/staged changes.
  2. Check remote tracking and push with `-u` if needed.
  3. Run `git log` and `git diff main...HEAD` for full commit history since divergence.
  4. Analyze: List commits, summarize changes (new feature, etc.), brainstorm purpose/impact, check for sensitive info.
  5. Draft summary: 1-2 bullet points focusing on "why", clear and accurate.
  6. Use `gh pr create` with HEREDOC for body (e.g., `gh pr create --title "Title" --body "$(cat <<'EOF'\n## Summary\n- Bullet 1\n- Bullet 2\nEOF\n)"`).
- **PR Reviews:** Ensure changes are tested, linted, and follow code style before merging.
- **No Direct Merges:** Use GitHub interface or CLI for PR merges to maintain history.

## Merging and Cleanup
- **Merge Strategy:** Prefer rebase for linear history—replay commits on main to avoid merge commits, keeping the log clean and sequential. This maintains linearity while allowing GitHub to track merged PRs.
- **Post-Merge Actions:**
  1. Switch to main: `git checkout main`.
  2. Merge: `git rebase <branch>` or use GitHub's rebase merge option.
  3. Push: `git push origin main`.
  4. Delete branches: `git branch -d <branch>` locally and `git push origin --delete <branch>` remotely.
- **Conflict Resolution:** If conflicts arise during rebase, resolve manually and test thoroughly before continuing.
- **PR History Visibility:** Even with rebase and deleted branches, GitHub's PR tab shows all merged PRs, including branch names, commits, and details for traceability.

## Additional Rules
- **Testing:** Run `bun run test` after changes; include tests for new features/bug fixes.
- **Documentation:** Update this file as practices evolve.
- **Exceptions:** Only bypass rules with explicit user approval for urgent fixes.

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