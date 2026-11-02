---
name: engineer-workflow
slug: engineer-workflow
description: Use when assigned an implementation subtask - setting up the branch, making the smallest complete change, running tests, self-reviewing the diff and handing off to review. Do not use for planning features or reviewing other people's code.
---

# Engineer: implement one subtask well

## 1. Before writing code

- Read the subtask, its acceptance criteria and the Tech Lead's plan on the parent task.
- If something needed to finish is missing or contradictory, ask in the task before coding.
- Check the working tree is clean: `git status`. If it is not clean and the changes are not yours, stop and report.
- Create the branch from the latest dev: `git switch dev`, `git pull`, then `git switch -c feat/<task-id>-<slug>` (or `fix/...`).
  If `dev` does not exist yet, stop and ask the Tech Lead; never branch from `main`.

## 2. Implement

- Make the **smallest complete change** that satisfies the subtask. No drive-by refactors,
  no renaming, no reformatting of untouched code.
- Follow the existing style, structure and libraries of the project. Do not add a dependency
  unless the plan says so.
- Preserve existing comments and behaviour you were not asked to change.
- Add or update tests for the behaviour you changed when the project has tests.
- Commit in small steps with `type(scope): summary` messages.

## 3. Verify before handoff

Run, in this order, and keep the outputs short in your notes:
1. The project's build or type check, if it has one.
2. The tests related to your change, then the full test suite if it is reasonably fast.
3. A manual check of the acceptance criteria you cover, when it can be done from the command line.

Then self-review: read `git diff dev...HEAD` top to bottom and check:
- [ ] Nothing unrelated changed
- [ ] No debug prints, commented-out code, TODOs without a task
- [ ] No secrets, absolute personal paths or machine-specific config committed
- [ ] Error cases handled, not just the happy path
- [ ] Names are clear; a reviewer can understand the change without asking you

## 4. Open a pull request (if the project has a GitHub remote)

Skip this section when `git remote -v` shows no GitHub remote.

1. Update from dev before pushing: `git fetch origin` then `git rebase origin/dev` (only on your own
   branch that nobody else has pulled; if it was already pushed and reviewed, use `git merge origin/dev` instead).
   Re-run the tests after updating.
2. Push your branch: `git push -u origin <your-branch>`.
3. Open a **draft** PR against dev (never against main):
   `gh pr create --draft --base dev --head <your-branch> --title "<type>(<scope>): <summary>" --body-file <file>`
   Write the body to a temporary file first (outside the repo, or delete it before committing) with:
   ```
   ## Zadanie
   <link or id of the Paperclip task>
   ## Co zmieniono
   - ...
   ## Jak sprawdzić
   - komendy testów + wynik
   ## Ryzyka
   - ... / brak
   ```
4. Mark it ready for review: `gh pr ready <number>`.
5. Put the PR URL in your handoff comment.

Never run `gh pr merge`. The Tech Lead merges into dev; the Board merges dev into main.

## 5. Hand off

Leave the handoff comment (format from dev-team-conventions) including the exact test commands,
their result and the PR URL if there is one, then request review from the Reviewer agent and set the subtask to in review.

## When review or QA sends it back

- Address every comment: fix it, or reply with a short reason why not
  (on GitHub: `gh pr comment <number> --body "..."`).
- Push fixes as new commits to the same branch (do not squash or force-push during review),
  then re-request review. The PR updates automatically.

## Never

- Commit to `dev` or `main`, force-push, open a PR against `main`, or merge your own branch.
- Mark a subtask done yourself; Reviewer and QA close the loop.
- Disable, skip or delete failing tests to make the suite pass.
