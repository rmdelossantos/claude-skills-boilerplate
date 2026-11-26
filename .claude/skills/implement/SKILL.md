---
name: implement
description: "Implements an approved plan test-first (Red → Green → Refactor), one behavior at a time, in the current branch. Use when the user asks to implement a plan they've reviewed, or invokes /implement. Called by /ship's implement() step."
---

## Goals

Definition of done: every behavior in the plan is built test-first and the full suite is green.

- [ ] Plan read in full; nothing implemented that the plan doesn't call for
- [ ] Every behavior went Red → Green → Refactor, with a failing test run seen before any implementation code
- [ ] Tests follow the Testing Conventions (AAA, one assertion where possible, behavior-describing names)
- [ ] Full test suite (plus type-check and lint, where the project has them) passes at the end
- [ ] Final report given (see `report()`)

## Input

- `$plan_file` - path to the plan. When omitted, use the plan in context (conversation, or a `### Plan` PR comment on the current branch's PR)
- `$scope` - optional; implement only part of the plan (eg `PR-A`, `steps 1-3`)

No plan found → stop and suggest `/plan-draft`. Don't invent one.

## Workflow

```pseudocode
begin({ $plan_file, $scope }) {
  read plan (limited to $scope)
  if (on the default branch) {
    create a branch first: `<handle>/<three-word-max>`
  }

  behaviors = split plan into testable behaviors
  create RED / GREEN / REFACTOR todos for each behavior

  for each behavior {
    tdd(behavior)
  }

  run full suite + type-check + lint
  report()
}
```

Leave changes uncommitted unless the user or calling skill says to commit. `/ship` commits and pushes after this skill returns.

### tdd(behavior)

Red → Green → Refactor, one behavior at a time. The todos look like:

```
- [ ] RED: Write failing tests for login
- [ ] GREEN: Implement login to pass tests
- [ ] REFACTOR: Clean up login implementation
- [ ] RED: Write failing tests for logout
- [ ] GREEN: Implement logout
- [ ] REFACTOR: Clean up
```

Each phase starts with an explicit prompt, given verbatim (to yourself, or to the subagent doing the work). Don't merge phases.

**RED**

> Write a FAILING test for [behavior]. Do NOT write the implementation yet. The test should fail because the function/method doesn't exist.

- Run the tests and confirm they fail for that reason, not because of a syntax error, bad import path, or broken fixture.
- Keep the failing output; GREEN can't start until you've seen it.

**GREEN**

> Now implement the minimum code to make these tests pass. Only write enough code to pass the current tests, nothing more.

- Run the full suite: the new tests pass and nothing else broke.
- Don't add behavior that no test covers. If more behavior is needed, it starts its own RED.

**REFACTOR**

> Refactor the implementation to improve code quality. Tests must stay green after refactoring. Focus on: [readability / performance / removing duplication].

- Pick the focus that fits what GREEN left behind.
- Run the suite again. It must stay green, and test files change only to remove duplication.

#### Testing Conventions

- Arrange-Act-Assert (AAA) structure.
- One assertion per test when possible.
- Test names describe behavior, eg `should_return_empty_when_no_items` (adapt the casing to the language's convention).
- Use the project's existing test framework and layout. If the project has none, set up its idiomatic one first, as part of the first RED.

When a change genuinely can't be tested first (pure config, docs, generated code), list it in the report instead of skipping silently.

### report()

End with a concrete report:

- Behaviors implemented, each with its test name(s)
- Files changed
- Test, type-check, and lint results (actual output summary, not "should pass")
- Deviations from the plan, and why
- Anything not tested first, and why
- Suggested next steps when run standalone: `/tidy-branch`, `/tidy-prose`, `/pr-autofix`
