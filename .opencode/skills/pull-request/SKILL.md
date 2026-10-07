---
name: pull-request
description: Creates a GitHub pull request from current changes. Use when asked to open a PR, publish a branch, or ship completed work.
metadata:
   version: 2.0.0
---

# Create GitHub Pull Request from Current Changes

## Role

You are an expert Git and GitHub automation assistant. Your goal is to
turn local changes into a published GitHub pull request.

## Prerequisites

- Terminal access
- File reading capabilities
- `gh` CLI installed and authenticated
- Skill `git-conventions` available (loaded automatically by the agent)

## Instructions

When the user asks you to create a branch, commit changes, and open a
GitHub pull request:

### 1. Load `git-conventions`

**Before doing anything else**, invoke the `git-conventions` skill. It
contains the rules for branch naming, Conventional Commits, atomic
commits, and PR description templates. Follow them strictly.

Do not proceed without it.

### 2. Analyze current changes

- Run `git status`, `git diff`, `git diff --staged` to inspect changes.
- Run `git remote -v` to determine the project origin.
- Based on changed files, determine:
  - an appropriate branch name (following `git-conventions`),
  - a PR title (Conventional Commits format),
  - a commit message (Conventional Commits format).

### 3. Create branch, commit, and push

Using the rules from `git-conventions`:

```bash
git checkout -b <branch-name>
git add .
git commit -m "<commit-message>"
git push -u origin <branch-name>
```

If changes cover multiple independent concerns, make multiple atomic
commits as `git-conventions` requires.

### 4. Create the GitHub Pull Request

- Determine the default target branch:
  ```bash
  git symbolic-ref refs/remotes/origin/HEAD
  ```
- Create the PR using the template from `git-conventions`:
  ```bash
  gh pr create --title "<title>" --body "<body>" --base <target-branch>
  ```

### 5. Handle authentication errors

If `gh pr create` returns an authorization error, ask the user to run
`gh auth login` and retry.

### 6. Report to user

Provide a direct web link to the created pull request.

## Verification

- [ ] `git-conventions` was loaded and followed.
- [ ] Branch name follows `<type>/<kebab-description>`.
- [ ] Commit messages follow Conventional Commits.
- [ ] Commits are atomic — one logical change per commit.
- [ ] PR title follows Conventional Commits.
- [ ] PR body follows the template from `git-conventions`.
- [ ] No secrets in diff (`git diff --cached | grep -i "password\|secret\|api_key\|token"` is empty).
- [ ] PR URL shared with the user.