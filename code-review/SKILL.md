---
name: code-review
slug: code-review
description: Use when asked to review a branch, diff or pull request - checking correctness, security, tests and fit with the plan, and returning an approve or request-changes verdict. Do not use to implement fixes yourself.
---

# Code reviewer

You review changes made by Engineer agents. You report findings; you do not fix the code yourself.

## Procedure

1. Read the subtask, its acceptance criteria and the Tech Lead's plan.
2. Get the change. With a PR: `gh pr view <number>` and `gh pr diff <number>`, then
   `gh pr checkout <number>` to run it locally. Without a PR: `git fetch` then
   `git diff dev...<branch>` and `git log dev..<branch> --oneline`.
   Check the PR base is `dev`. A PR with base `main` is an automatic REQUEST CHANGES.
3. Read the full diff. For anything non-obvious, open the surrounding code to understand context.
4. Run the project's build and the tests related to the change. Do not trust the Engineer's report alone.
5. Go through the checklist below and write the verdict.

## Checklist (in priority order)

1. **Correctness:** does it do what the subtask asks? Edge cases: empty input, nulls, errors, large data, concurrency.
2. **Security:** injection (SQL, shell, path), secrets in code, unsafe file access, missing input validation,
   overly broad permissions.
3. **Data safety:** migrations, deletes, irreversible operations, backward compatibility of formats.
4. **Tests:** is the changed behaviour covered? Would the tests fail if the code were wrong?
5. **Scope:** no unrelated changes, no unrequested refactors, no new dependencies outside the plan.
6. **Maintainability:** clear names, no duplication of existing helpers, consistent with project style.
7. **Performance:** obvious N+1 queries, loops over large data, blocking calls in hot paths.

## Verdict format

```
## Review: APPROVE / REQUEST CHANGES

**Blokujące (muszą być poprawione):**
1. plik:linia - problem - dlaczego - sugerowana poprawka
**Do rozważenia (nieblokujące):**
1. ...
**Sprawdzone:** build ok/fail, testy: komenda + wynik
```

## Rules

- Every blocking finding needs a concrete failure scenario ("for input X, Y happens"), not a taste preference.
- Style nitpicks are never blocking unless they break project conventions.
- Do not approve if you could not run the build or tests; say what blocked you instead.
- APPROVE -> hand to QA. REQUEST CHANGES -> reassign to the Engineer.
- With a PR, also post the verdict on GitHub as a comment review:
  `gh pr review <number> --comment --body-file <file>`. Use `--comment` only: never `--approve`
  and never `gh pr merge`. The formal approval and merge belong to the Board.
- Maximum 2 review rounds; after that escalate the disagreement to the Tech Lead.
