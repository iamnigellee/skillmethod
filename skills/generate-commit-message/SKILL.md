---
name: generate-commit-message
description: >
  Generates a conventional commit message by analyzing the current git diff,
  staged changes, and recent commit history to match the repository's existing
  style. TRIGGER when user asks to write a commit message, draft commit message,
  help me commit, what should I commit as, generate commit msg, summarize my
  changes for commit, conventional commit for this diff.
  SKIP when user already wrote a commit message and wants review or editing.
  SKIP when user asks about git history or understanding past changes.
  SKIP when user wants to amend, squash, or rebase commits.
  SKIP when user wants to execute git commit (this generates the message only).
---

# Generate Commit Message

## Critical Constraints

- NEVER execute `git commit`, `git add`, `git push`, or any state-changing git command.
- Output ONLY the commit message in a fenced code block. No explanation unless asked.
- If diff exceeds 2000 lines:
  `[SKILL_CANNOT_PROCEED: diff too large (N lines). Split into smaller commits.]`
- If no staged or unstaged changes exist:
  `[SKILL_CANNOT_PROCEED: no changes detected. Stage files with git add first.]`
- If only binary files changed:
  `[SKILL_CANNOT_PROCEED: only binary changes detected. Describe them manually.]`

## Context Acquisition (mandatory before generating)

1. Run `git diff --cached --stat` to check staged changes.
2. If staged changes exist, run `git diff --cached` for full diff.
   If nothing staged, run `git diff` for unstaged and note to user.
3. If diff exceeds 500 lines, use `--stat` summary only (note reduced precision).
4. Run `git log --oneline -10` to detect the repo's commit style.

## Style Detection

Read the 10 most recent commits and detect:

1. Does the repo use conventional commits? (`type:` or `type(scope):`)
   - If 3+ of 10 use conventional format → use conventional commits.
   - If fewer than 3 → match the majority style instead.
2. Capitalization: first letter uppercase or lowercase?
3. Mood: imperative ("add") vs past tense ("added")?
4. Typical subject length? Match it.

Do NOT force conventional commits on repos that use a different convention.

## Commit Type Detection

When using conventional commits, determine type:

```
New file or feature adding capability?
├─ Yes → feat
└─ No → Fixes broken behavior?
    ├─ Yes → fix
    └─ No → Restructure without behavior change?
        ├─ Yes → refactor
        └─ No → Only test files?
            ├─ Yes → test
            └─ No → Only documentation (.md/.txt/.rst)?
                ├─ Yes → docs
                └─ No → Build/CI config?
                    ├─ Yes → ci or build
                    └─ No → chore
```

## Scope Detection

1. All changes in one directory/module → use as scope: `feat(auth):`
2. Changes span 2 related modules → pick the primary one.
3. Changes span 3+ unrelated modules (no shared parent within 2 levels) →
   CHECKPOINT: ask user which scope, or suggest splitting the commit.

## Breaking Change Detection

Add `!` after type and `BREAKING CHANGE:` footer when ANY of:
- Public API signature changed (params added/removed/retyped)
- Configuration format changed incompatibly
- Database schema migration required
- Environment variable renamed or removed

CHECKPOINT: confirm with user before adding BREAKING CHANGE footer.

## Output Format

Single-line (default for simple changes):
```
type(scope): imperative subject under 72 chars
```

Multi-line (for complex changes or breaking changes):
```
type(scope): imperative subject under 72 chars

Concise WHY explanation. Max 2-3 sentences.

BREAKING CHANGE: what breaks and migration path
```

## Commit Type Reference

| Type     | When to use                              |
|----------|------------------------------------------|
| feat     | New feature visible to users             |
| fix      | Bug fix                                  |
| docs     | Documentation only (.md/.txt/.rst)       |
| style    | Formatting, no logic change              |
| refactor | Code restructure, no behavior change     |
| test     | Adding or fixing tests                   |
| chore    | Maintenance, dependencies, tooling       |
| ci       | CI/CD configuration                      |
| build    | Build system or external dependencies    |
| perf     | Performance improvement                  |

## What NOT To Do

- "Updated files" — too vague, describes what git already shows
- "Fixed stuff" — no specificity
- "WIP" — not a finished commit message
- "feat: add new feature to improve the user experience" — too long, too generic
- Do NOT list every file changed. Describe intent, not inventory.
- Do NOT add multi-paragraph body for a one-line change.
- Do NOT include the diff content in the message.
- Do NOT generate multiple alternative messages unless user asks.

## Edge Cases

- Only lockfile changed (package-lock.json, yarn.lock, Cargo.lock):
  → `chore(deps): update lockfile`
- Only .gitignore or config dotfiles:
  → `chore: update gitignore` or `chore: update eslint config`
- Single typo fix in source code:
  → `fix`, not `docs` (docs is for documentation files only)
- File rename/move only:
  → `refactor(scope): rename X to Y`
- Multiple unrelated changes (when user insists on one commit):
  → Use most significant change as subject, mention others in body

## Checkpoint Conditions

Ask the user before generating in these situations:
- Changes span 3+ unrelated scopes
- Both staged AND unstaged changes exist: "Generate for staged only, or all?"
- Breaking change detected: "This appears breaking. Confirm?"

## Final Reminders

- This skill generates the message ONLY. Never run git commit.
- Never stage files. Never push. Never modify git state.
- If diff exceeds 2000 lines: `[SKILL_CANNOT_PROCEED: diff too large]`
- If no changes detected: `[SKILL_CANNOT_PROCEED: no changes detected]`
- Output the commit message in a fenced code block. Nothing else unless asked.
