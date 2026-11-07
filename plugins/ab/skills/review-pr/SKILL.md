---
name: review-pr
description: Review a pull request before merge, step by step - minimal diff, tests, readability, code-review:code-review, manual test, then the PR description. Use when the user asks to review, check, or prepare a PR for merge.
argument-hint: "[PR number; default: the PR of the current branch]"
---

# PR review

Review the PR in the steps below, in this order. Do one step at a time.

Before step 1, read:

- `CLAUDE.md` and the files it references (style guide, rules);
- the task, plan, spec, or issue that the PR implements. If you cannot find it, ask the user.
- the project commands that run the tests and the linters (`Makefile`, `pyproject.toml`, `package.json`, README). If there are none, ask the user.

Rules for all steps:

- In each step, first list the findings: file, line, problem, proposed fix. Do not change the code before the user approves the list. The user can reject a finding.
- Apply the approved fixes. Then run the tests and the linters. All must pass before the next step.
- If two fixes are possible, select the shorter one, unless it is less readable.
- Commit each step separately. Push only when the user tells you to.

## 1. Scope: minimal diff

Get the diff: `gh pr diff <PR>`. Compare it with the task.

Find:

- Code, files, or text that the task does not need. Remove them.
- Duplicate code or text. Reuse the existing code.
- Comments that explain what the code does, repeat the code, or have no information. Remove them.
- Complex or unclear logic. Make it direct: fewer branches, fewer layers.

Readability must not decrease.

## 2. Tests

Check each test that the PR adds or changes:

- The test checks code that this PR adds or changes.
- The test is short and minimal.
- The test does not check a self-evident thing or a standard or third-party library.
- No two tests check the same path. If two tests do, keep the higher-level test.
- No duplicate setup or data between tests. Use fixtures and parametrized tests.

Do a mutation check for each important code path: break the code (remove a line, invert a condition), run the tests, then restore the code. At least one test must fail. If no test fails, add a test or report the gap. If a test does not fail for any change in the code it tests, remove the test.

Report important code that has no test: failure paths, timeouts, bad input, out-of-range values, reconnects.

## 3. Readability

Read the full diff as a person who does not know the task. Check that the code and the text:

- are short and simple;
- have no contradictions between code, comments, and docs;
- have no duplication;
- do not explain self-evident things;
- have no filler.

Write prose (comments, docs, messages) in ASD-STE100 Simplified Technical English: short sentences, active voice, imperative for instructions, one meaning per word. If `CLAUDE.md` sets other language rules, use them.

If two texts contradict, fix the contradiction with the shorter change.

## 4. Code review

Invoke the `code-review:code-review` skill with the PR number. Show its findings to the user. Apply only the findings that the user approves, then run the tests and the linters.

## 5. Manual test

Do this step after all code changes: a later change makes the test result old.

If the project has a manual or hardware test for this kind of change, ask the user to run it. If a fix is necessary, apply it, run the tests and the linters, and ask the user to run the manual test again. Keep the result for step 6.

## 6. PR description

Do this step last: steps 1-5 can change the diff.

- Make sure that the description agrees with the final diff.
- Add the result of the manual test from step 5.
- Apply the readability rules of step 3 to the description, ASD-STE100 included.

Show the new text to the user. Update the PR with `gh pr edit` after the user approves the text.
