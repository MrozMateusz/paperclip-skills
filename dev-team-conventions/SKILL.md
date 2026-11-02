---
name: dev-team-conventions
slug: dev-team-conventions
description: Use on every software development task in this company - shared rules for branches, commits, task handoffs, status updates, Windows environment and language. Do not use for non-engineering work.
---

# Dev team conventions

Shared rules for every member of the development team (CEO, Tech Lead, Engineer, Reviewer, QA).
Follow them unless a task explicitly says otherwise.

## Team and flow of work

1. **Board (Mateusz, human)** sets goals and approves anything risky.
2. **CEO / Product Owner** turns goals into clear, testable tasks with acceptance criteria.
3. **Tech Lead** plans the technical approach, splits work into small subtasks, assigns them.
4. **Engineer** implements one subtask at a time on its own branch.
5. **Reviewer** reviews the diff. **QA** verifies behaviour against acceptance criteria.
6. **Tech Lead** closes the parent task when all subtasks pass review and QA.

A task is never "done" because the author says so. It is done when Review and QA both passed.

## Language

- Write task titles, comments, plans and reports for the Board **in Polish**.
- Write code, identifiers, commit messages, code comments and READMEs **in English**, unless the project already uses Polish.

## Environment (important)

- The host is **Windows 11**. Commands run in a Windows shell. Prefer cross-platform commands
  (`git`, `npm`, `python`, `node`) and Windows paths (`M:\Projects\...`). Do not assume bash, `sudo`, `apt` or `/usr/...`.
- Never run commands that need administrator rights. If something needs admin, stop and ask the Board.
- Never touch files outside the project working directory.
- Never commit secrets (`.env`, API keys, tokens). If you find one in the repo, report it, do not copy it anywhere.

## Git rules

- Branch model: **`dev`** is the integration branch for all team work. **`main`** is the release branch and
  belongs to the Board only. Nobody on the team commits, pushes or merges to `main`, ever.
- Never commit directly to `dev` either. Branch off the latest `dev`: `feat/<task-id>-<short-slug>`,
  `fix/<task-id>-<short-slug>`, `chore/<task-id>-<short-slug>`.
- Small, focused commits. Message format: `type(scope): summary` (types: feat, fix, refactor, test, docs, chore).
- Never force-push, rewrite shared history, or delete branches you did not create.
- All PRs target `dev`. A PR is merged into `dev` **only by the Tech Lead**, and only after Review APPROVE and QA PASS.
- `dev` -> `main` is done by the Board when they decide to release.

## GitHub (gh CLI)

The host is logged in to GitHub through the `gh` CLI with the **Board's personal account**.
That account can do far more than the team needs, so these limits are absolute:

- Allowed: `git fetch`, `git pull`, `git push -u origin <your-branch>`, `gh pr create`, `gh pr view`,
  `gh pr diff`, `gh pr checks`, `gh pr comment`, `gh pr review --comment`, `gh pr edit` on PRs the team opened.
- Only the Tech Lead may run `gh pr merge`, and only for PRs whose base is `dev` (see tech-lead-planning).
- **Forbidden for everyone:** merging anything into `main`, any PR with base `main`, pushing to `main`/`master` or `dev`, `git push --force` / `--force-with-lease`,
  deleting remote branches (except the Tech Lead's `--delete-branch` when merging into dev), `gh repo create/delete/edit`, `gh release`, changing repo settings,
  secrets, webhooks or collaborators, and any `gh api` call that writes (POST/PATCH/PUT/DELETE).
- Work only in the repository of the current project. Never touch other repositories of the account.
- If the project has no GitHub remote (`git remote -v` is empty), skip all GitHub steps and work with local branches only.
- If a `gh` command fails with an auth error, stop and ask the Board; do not try to log in yourself.

## Task handoff format

When you hand work to someone else (or finish a task), leave a comment with exactly these sections:

```
**Co zrobiono:** 1-3 zdania
**Branch / commity:** nazwa brancha, krótkie hashe
**Jak sprawdzić:** komendy lub kroki do odtworzenia
**Ryzyka / otwarte pytania:** lista albo "brak"
**Następny krok:** kto i co
```

## When blocked

- Blocked for longer than one heartbeat by missing info -> ask a precise question in the task and mark it blocked.
- Do not guess requirements. Do not silently widen the scope of a task. Propose a new task instead.
- Two failed attempts at the same fix -> stop, write down what you tried, escalate to the Tech Lead.

## Cost discipline

- Read only the files you need. Search before reading whole directories.
- Do not run long or expensive commands (full builds, large test suites) more often than necessary.
- Prefer one good plan over many small speculative edits.
