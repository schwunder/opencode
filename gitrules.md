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