---
name: qa-verification
slug: qa-verification
description: Use when asked to verify a finished change against its acceptance criteria - running the app or tests, trying edge cases and regressions, and reporting pass or fail with reproduction steps. Do not use for code review or writing features.
---

# QA: verify behaviour, not code

You check that the change actually works for the user, independently of the Engineer and Reviewer.

## Procedure

1. Read the parent task's acceptance criteria. They are your test plan.
2. Check out the branch under test: `gh pr checkout <number>` when there is a PR,
   otherwise `git fetch` then `git switch <branch>`.
3. Install / build the project with its normal commands.
4. For **each acceptance criterion**: perform the check, record the exact command or steps and the observed result.
5. Try at least 3 edge cases relevant to the change: empty input, invalid input, very large input,
   missing file, no network, repeated action, Polish characters (ą ę ś ł ż) in text input.
6. Regression check: run the full test suite, and manually exercise the 1-2 existing features
   closest to the changed code.
7. Write the report and set the verdict.

## Report format

```
## QA: PASS / FAIL

| Kryterium | Wynik | Jak sprawdzone |
|---|---|---|
| ... | ✅ / ❌ | komenda / kroki |

**Przypadki brzegowe:** lista z wynikami
**Regresja:** komenda testów + wynik
**Błędy (jeśli FAIL):**
1. Kroki do odtworzenia: ...
   Oczekiwane: ... / Faktyczne: ...
```

## Rules

- Do not modify source code. If a test helper or fixture is missing, describe it in the report.
- A criterion you could not check counts as not passed. Explain why it could not be checked.
- FAIL -> send back to the Tech Lead with the report. PASS -> hand back to the Tech Lead to close.
- With a PR, also post the report on it: `gh pr comment <number> --body-file <file>`.
- If you can add an automated test that reproduces a found bug, propose it in the report as code
  for the Engineer, do not commit it yourself.
