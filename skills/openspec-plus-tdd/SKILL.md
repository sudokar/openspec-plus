---
name: openspec-plus-tdd
description: "MANDATORY skill that activates whenever code is written to implement an OpenSpec change task. Triggers: openspec-plus-apply is active, /opsx-apply is running, the user is implementing tasks from an OpenSpec change, an implementer subagent dispatched by openspec-plus-apply is starting work, or the user invokes phrases like 'TDD for the change', 'implementing change tasks', or 'writing tests for spec scenarios'. Load before any production code is written for an OpenSpec change. Enforces strict RED-GREEN-REFACTOR per test (acceptance, unit, edge case, helper, error path). Iron Law: NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST. Gherkin scenarios in spec.md define requirements that must be covered; additional tests discovered during implementation follow the same per-test cycle and become mandatory once identified."
version: 1.1.0
priority: high
disable-user-invocation: true
---

# OpenSpec Plus TDD

## Mission

Strict RED-GREEN-REFACTOR per test for OpenSpec change implementation. Every test — whether derived from a Gherkin scenario relevant to the slice in `spec.md`, written for a unit, edge case, helper, or error path — goes through its own atomic cycle before the next test begins. Production code exists only to make a previously-failing test pass. Surgical changes, simplicity first, no speculative abstractions, every changed line traces to a slice task.

Gherkin scenarios in `spec.md` define the **requirements** the slice must satisfy. They guide what to test but do not limit the test set — the implementer writes every valid test (acceptance, unit, edge case, helper, error path) discovered during implementation. Tests are not fully enumerated upfront; they emerge incrementally as production code takes shape. Every Gherkin scenario must be covered by the end, but granular tests discovered along the way are equally mandatory. Every test follows the same cycle.

Loaded by `openspec-plus-apply` (subagent prompt + inline mode) before any code is written.

---

> **RIGID. NEVER write production code before a test fails for the right reason. NEVER write tests for multiple cases before the first one is GREEN. NEVER skip the REFACTOR assessment. NEVER mark a task `[x]` while a relevant test is failing or skipped. NEVER add comments for non-complex logic. NEVER refactor code outside the slice. NEVER write any code (test or production) before reading the project's referenced coding/testing standards. NEVER ship without covering every Gherkin scenario in spec.md with at least one test. Letter and spirit are the same.**

**Red flags — STOP, you are about to violate this skill:**

- "I'll write the test after, it's faster"
- "Too simple to need a test"
- "Manually verified, that's enough"
- "Gherkin scenario is vague, generic test is fine"
- "Skip this failing test, circle back later"
- "Mark `.skip` to unblock the slice"
- "While I'm here, clean up the adjacent code"
- "Add an interface in case we swap implementations"
- "Short comment explains the obvious"
- "Error handling for cases that can't happen"
- "Test passed first run, must be right"
- "Let me write tests for all the cases first, then implement"
- "Test 1 done — I have a clear picture, let me write all the rest at once"
- "I have a clear picture of all 5 cases — let me write them all"
- "Writing one test at a time is slower"
- "These cases are related, I'll batch them"
- "The code I'm about to write covers test 2 anyway, no need to write its test separately first"
- "Acceptance tests cover the happy path — skip the unit/edge tests"
- "Scenarios covered, no need to add granular tests even though the helper has edge cases"
- "Nothing to refactor, skip the assessment"
- "I know the project conventions, no need to re-read AGENTS.md"
- "AGENTS.md has many rules — I'll apply the ones that feel relevant"

None justify production code without a red test, ignored failures, speculative abstractions, comments on obvious code, or scope expansion.

---

## The Iron Law

```
NO PRODUCTION CODE WITHOUT A FAILING TEST
```

Every test — acceptance, unit, edge case, helper, error path — must be observed to fail for the right reason before the production code that makes it pass is written. Test not observed to fail = test proves nothing. Code written first = delete it, start over. No exceptions without explicit user permission.

### Mechanical Constraint: One Test Per Edit

"One test at a time" is a mechanical rule, not a guideline:

* Each edit to a test file MUST introduce **exactly one** new test function/block (`test()`, `it()`, `t.Run()`, `def test_`, etc.).
* If you are about to write an edit that adds 2 or more new test blocks — **STOP. You are batching.** It does not matter that the tests "correspond to scenarios" or are "in order." Split the edit into separate cycles.
* After each single-test edit, you MUST run the test suite and observe the failure before writing any production code. Then complete GREEN and REFACTOR for that one test before editing the test file again.
* "Building up the test suite" is not a justification. The suite grows by exactly one test per cycle. There is no shortcut.
* Writing tests "in order" does not equal TDD. TDD means each test is RED → GREEN → REFACTOR **individually** before the next test is written.

**Litmus test before every test-file edit:** Count the new `test()` / `it()` / `t.Run()` / `def test_` blocks you are about to add. If the count is not exactly 1, delete and rewrite.

### Mechanical Constraint: Only the Failing Test's Code Path

Each edit to production code MUST introduce **only the code path that the current failing test exercises**. Before writing production code, ask: "Which branch, condition, or logic does the failing test's assertion require?" Write that and nothing else.

* If the failing test asserts a valid-login response, write only the valid-login path — do not also write the invalid-login path, the rate-limit path, or the session-expiry path.
* If the failing test asserts a specific error message, write only the code that produces that error — do not also handle other error cases.
* Every line of production code you write must be necessary for the current failing test to pass. Lines not necessary for the current test are speculative — delete them.

**Litmus test before every production-code edit:** For each line you are about to write, ask "if I remove this line, does the current test's assertion still fail for the same reason?" If yes — the line is unnecessary for this test. Remove it; it belongs in a future cycle.

### Test Coverage

Every Gherkin scenario relevant to the slice in `spec.md` must be covered by at least one test — but Gherkin scenarios are requirements, not a ceiling. Unit, edge-case, helper, and error-path tests discovered during implementation are equally mandatory once identified. The test set grows incrementally: after each RED-GREEN-REFACTOR cycle, ask "what is the next test this code needs?" — which may be the next Gherkin scenario or a granular test the current cycle revealed. Same cycle for every test, regardless of origin.

---

## Inputs

* Slice tasks (`tasks.md`)
* Slice spec requirements + Gherkin scenarios (`spec.md`)
* Slice design decisions (`design.md`)
* Project standards (`AGENTS.md` / `CLAUDE.md` / `GEMINI.md` if in context)
* Existing code in slice's affected files

NEVER read source outside the slice's affected files.

---

## Workflow

```text
Phase 0: Pre-RED — read project's coding/testing conventions; follow strictly

Phase 1: Read Gherkin scenarios in spec.md to understand requirements.
  Identify the first test to write — typically the simplest Gherkin
  scenario or the smallest unit of behavior. Do NOT enumerate all
  tests upfront; the full test set emerges incrementally.

Phase 2+: For each test, ONE AT A TIME:
  Do NOT begin the next test until the current one terminates at
  "Test Complete". After each cycle, decide the next test: the next
  uncovered Gherkin scenario, or a granular test this cycle revealed.

After all tests complate, requirements are met AND no further tests are needed, run
the slice's pre-mark gate.
```

### Per-Test State Machine (MANDATORY)

Every test traverses this cycle end-to-end before work begins on the next. Atomic per test — no shortcuts, no batching, no skipping nodes. Applies to all test types (acceptance and granular).

**Per-test cycle:** START → RECORD STATE BEFORE (file path + count + names) → RED (write ONE failing test K) → VERIFY-RED (fails for expected reason? no → fix test, retry) → GREEN (minimum production code for K only) → VERIFY-GREEN (K passes, others green, output pristine? no → fix production code, retry) → REFACTOR ASSESS (needed? yes → act, verify green, revert if broken; no → record "not needed — reason") → RECORD STATE AFTER (count = previous + 1) → TEST K COMPLETE → return to START for K+1 (or end if all Gherkin scenarios covered AND no further tests needed).

The cycle forbids:

* Starting test K+1 before K reaches COMPLETE
* Skipping REFACTOR assessment — every test passes through it
* Skipping state recording — audit trail is mandatory
* Ending slice with uncovered Gherkin scenarios

---

## Concrete Pattern: WRONG vs RIGHT

This is the single most-violated rule. Read both examples carefully.

### WRONG — Batching (the model's training default)

```
Implementer opens empty test file.
Writes test 1, test 2, test 3, test 4, test 5 in one pass.
Runs tests — all 5 fail.
Writes production code covering all 5 cases in one pass.
Runs tests — all 5 pass.
Reports DONE.
```

This is NOT TDD. Each test passed immediately when production code arrived. You never observed test 1 failing in isolation. You never refactored after each green. You wrote the whole solution in your head and dumped it onto disk.

The end state (5 tests, 5 features) is identical to RIGHT — but the discipline is absent. The reviewer cannot tell from end state alone, but YOU know you batched.

### RIGHT — One Test At A Time

```
State: 0 tests.

Test 1 (acceptance — Gherkin "valid login"):
  Write test 1 (1 test, 1 failing). RED: "expected `Email required`, got undefined" ✓
  Write minimum production. GREEN: 1 passing, pristine ✓
  Refactor: no duplication, names clear → "not needed."

Test 2 (acceptance — Gherkin "invalid password"):
  Write test 2 (2 tests, 1 failing). RED: "expected `Invalid password`, got `Internal error`" ✓
  Write minimum production. GREEN: 2 passing ✓
  Refactor: extracted `mapAuthError` helper. Tests green.

Test 3 (unit — edge case for `mapAuthError(null)`):
  Write test 3 (3 tests, 1 failing). RED: "expected `Invalid input`, got TypeError" ✓
  Add null guard. GREEN: 3 passing ✓. Refactor: not needed.

[repeat for each test...]
```

Tests 1-2: Gherkin scenarios (mandatory acceptance). Test 3: implementer-initiated edge case (encouraged granular). All follow the same per-test cycle.

If you find yourself thinking *"I know all 5 cases, let me write them all at once"* — STOP. That is the violation. Delete what you just wrote. Restart from test 1.

### Why "I'll write all the tests, then implement" is wrong

* Test 2 might pass immediately when you implement test 1 — you'd never know if test 2 actually tests what you think.
* No checkpoint forces you to confront edge cases per test. Edge cases get glossed.
* You miss refactor opportunities that emerge between tests.
* The discipline is the value, not the end state.

---

## Phase 0: Pre-RED — Read Referenced Conventions

Before ANY code (mandatory, once per slice):

1. `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` (or equivalents at project root, `.claude/`, `.opencode/`, `docs/`)
2. Follow references inside those files to other docs (coding standards, testing conventions, patterns)
3. Slice's affected files — absorb local style

These files are the contract — follow every documented rule strictly, end-to-end (no cherry-picking). Re-read per slice (files may have been updated). Do NOT proceed to Phase 1 before reading is done.

---

## Phase 1: RED — Failing Test (One At A Time)

The test you write at this phase is one of two kinds:

**1. Acceptance test — derived from a Gherkin scenario requirement.**

A Gherkin scenario in `spec.md`:

```gherkin
#### Scenario: User logs in with valid credentials
GIVEN a user account exists with email `alice@example.com` and password `correct-pw`
WHEN the user submits the login form with those credentials
THEN the response sets a session cookie
AND the user is redirected to `/dashboard`
```

Translate directly into one minimal acceptance test:

```typescript
test('logs in with valid credentials', async () => {
  await createUser({ email: 'alice@example.com', password: 'correct-pw' });

  const response = await submitLogin({ email: 'alice@example.com', password: 'correct-pw' });

  expect(response.headers['set-cookie']).toMatch(/session=/);
  expect(response.status).toBe(302);
  expect(response.headers.location).toBe('/dashboard');
});
```

**2. Granular test — discovered during implementation for a unit, edge case, helper, or error path.**

Example: while implementing the login above, the implementer factors out a `mapAuthError` helper. They add a unit test for it:

```typescript
test('mapAuthError handles null input', () => {
  expect(() => mapAuthError(null)).toThrow('Invalid input');
});
```

This test was not derived from a Gherkin scenario — it was added because the helper's null case needs fast-feedback coverage. It follows the same cycle.

Rules (both kinds):

* One test at a time — never batch. Test name describes behavior, not implementation.
* Real code paths; mocks ONLY when dependency unavailable. Test the OUTCOME, not call sequence.
* Acceptance: translate Gherkin faithfully. Granular: state the unit's contract explicitly.

---

## Phase 2: VERIFY-RED — Watch It Fail Correctly

**MANDATORY. NEVER SKIP.**

Run the test. Confirm:

1. Test FAILS (not errors, not passes).
2. Failure message matches what scenario implies.
3. Failure is because feature is missing — not typo, not missing import, not setup bug.

Test passes immediately → either the test is wrong, or a prior GREEN over-produced code that already covers this case. Investigate which: if the test is valid and prior production code already satisfies it, this is a TDD violation in the earlier cycle. Return to that earlier GREEN, remove the unnecessary production code, and re-run until the current test is RED for the right reason. Do NOT accept accidental coverage and move on.
Test errors (compilation failure, missing type, unresolved import) → add **only the minimum scaffolding** to make the test compile and run: stub functions that return zero/empty values, empty types/interfaces, missing imports. Scaffolding has no logic, no branching, no real behavior — just enough structure for the test to execute and fail for the behavioral reason. Re-run until the test fails with the expected assertion message, then stop — Phase 3 (GREEN) is where real production code begins.

---

## Phase 3: GREEN — Minimum Production Code

Write **only the minimum production code necessary to make the current failing test pass** — not the whole code path you expect the feature will eventually need.

Execute GREEN in this order:

1. State the current test's **single missing behavior** in one sentence.
2. State the exact branch, condition, helper, or line(s) you are about to add, and why each is necessary for this test.
3. Add only those line(s), then run the current test.
4. If the current test passes, continue to VERIFY-GREEN. If a later test turns GREEN on its first run because of this edit, treat that as automatic over-production and revise the earlier production edit until the later test is RED for the right reason.

* Only the branch/logic the current test's assertion requires — if the test checks a valid login, do not also write the invalid-login branch
* No features beyond what the failing test exercises
* No abstractions for single-use code
* No flexibility/configuration the test didn't ask for
* No error handling for cases the test doesn't cover
* Match existing patterns

If you are writing code because "the next test will need it," STOP. That code belongs in the next cycle, not this one.

If multiple new lines are needed, each line must satisfy the necessity check from the Mechanical Constraint above. If any line is not required for the current test to go GREEN, do not write it.

If the next test passes immediately after this GREEN, you over-produced here. The code you wrote covered more than the current test required. This is a TDD violation, not an efficiency win. Remove the extra code and restore the missing RED.

---

## Phase 4: VERIFY-GREEN — Watch It Pass

**MANDATORY.**

1. Test passes.
2. Other tests in the file still pass.
3. Tests for slice's other scenarios still pass.
4. Output pristine — no warnings, errors, deprecations.

Test fails → fix production code, NOT the test. Test is the source of truth.
Unrelated tests fail → fix now, before next test.

---

## Phase 5: REFACTOR — Mandatory Assessment, Conditional Action

REFACTOR is NOT optional. After every GREEN, you MUST perform an explicit refactor assessment on **both test and production code** introduced or modified in this cycle (within the slice's files only).

### 5.1 Assessment (always — no cherry-picking)

Enumerate, then judge. Do not skip categories or pick only the obvious ones.

1. **Enumerate applicable clean code principles** — e.g. single responsibility, meaningful names, small focused functions, DRY, least surprise, command-query separation. List which apply to the code touched in this cycle.
2. **Enumerate applicable refactoring patterns** — e.g. extract method/function, inline variable, rename, replace conditional with polymorphism, introduce parameter object, pull up/push down. List which could apply.
3. **Enumerate applicable code smells** — e.g. duplication, long method, feature envy, data clumps, primitive obsession, shotgun surgery, speculative generality. List any present.
4. **Check against project conventions** — re-read Phase 0 conventions. List any deviations in test or production code.

Assess each enumerated item across both test and production code touched in this cycle. For each item, state: applies / does not apply, and if applies, whether action is needed.

Answer in the report: **Is refactoring needed?** (yes/no with one-sentence reason per finding)

If **no** → state explicitly: *"Refactor assessment: no refactoring needed — [reason]."* Then move to NEXT.

If **yes** → proceed to 5.2.

### 5.2 Action (only if assessment = yes)

Refactor test and/or production code respecting project conventions. Tests must stay green — run after every edit. NEVER add behavior, touch outside slice, or reformat adjacent code. If tests break → revert.

Record outcome (e.g., *"Extracted `parseAuthHeader` helper; renamed test to match convention; tests green"* or *"No refactoring needed — minimal, clear, non-duplicative"*).

---

## Phase 6: NEXT — Only Now

Only after the current test's REFACTOR assessment is recorded, decide the next test. Ask: "What is the next test this code needs?" — the answer may be the next uncovered Gherkin scenario, or a unit/edge/error test that the current cycle revealed (e.g., a helper with an untested null path, a branch with no coverage). Return to Phase 1 RED with that test.

NEVER skip ahead. NEVER write the next test while the current one is still in GREEN or REFACTOR phase.

The slice is done when:
* Every Gherkin scenario in spec.md has at least one passing test, AND
* Every test discovered during implementation is passing, AND
* No further tests are needed (no untested branches, edge cases, or error paths remain), AND
* All tests went through the per-test cycle individually.

---

## Implementation Principles (apply throughout)

| Principle | TDD application |
|---|---|
| Think Before Coding | Read scenario or unit contract carefully. State assumptions. Ambiguous → ASK before writing the test. |
| Simplicity First | One test at a time (acceptance OR granular). Minimum production change. No speculative abstractions. |
| Surgical Changes | Every changed line traces to a slice task. Don't refactor adjacent code. Match existing style. |
| Goal-Driven Execution | The current test (Gherkin scenario OR unit contract) IS the verifiable goal. Loop independently until the test passes. |

---

## Code Style Rules — Code As Documentation

Code explains itself through good names, small focused functions, and clear structure.

* **Self-documenting.** Names describe intent. Functions do one thing. Structure makes flow obvious.
* **Comments only when:** genuinely non-obvious algorithm, external-constraint workaround, or counter-intuitive tradeoff. Refactor before commenting — if better naming/structure removes the need, do that first.
* **Never:** describe obvious behavior in comments; leave commented-out code, TODO, FIXME.
* **Testable** (hard to test = hard to use), **readable** (one mental load per function), **maintainable** (one responsibility per file).

---

## Verification Gates

**Per-test:** Pre-RED done ✓ RED observed ✓ GREEN passes + others green + pristine ✓ Minimum code ✓ No unnecessary comments ✓ No adjacent code touched ✓ REFACTOR assessed ✓ — Cannot check all? Restart from RED.

**Per-slice:** Every Gherkin scenario covered ✓ All discovered tests pass ✓ No untested branches/edge cases/error paths remain ✓ Each test went through its own cycle (no batching) ✓

---

## When Stuck

| Symptom | Cause | Fix |
|---|---|---|
| Hard to write test | Interface hard to use | Simplify interface before writing test |
| Need to mock half the world | Code too coupled | Dependency injection or split unit |
| Test setup huge | Coupling | Extract helpers; still huge → simplify design |
| Scenario ambiguous | Spec gap | STOP. Ask user. Don't invent |
| Don't know what granular tests to add | Just-write-tests trap | Add tests for: non-trivial branches, null/empty inputs, boundary values, error paths the scenario doesn't cover. Skip if behavior is fully covered by acceptance test. |
| Test passes immediately | Feature exists or test wrong | Investigate; rewrite test |
| Can't make test fail right | Test wrong | Rewrite before any production code |
| Production code keeps growing | Over-engineering | What's the minimum that passes? |

---

## Anti-Patterns

NEVER: skip Pre-RED reading | batch tests | move to next test before current REFACTOR | skip REFACTOR assessment | production code before failing test | test after code works ("for coverage") | `.skip`/`.todo`/`xtest`/comment-out | suppress output | add comments on non-complex logic | leave TODO/FIXME/commented-out code | refactor adjacent code | features the test didn't request | abstractions for single use | error handling for untested cases | code written because a future test will need it | code that makes the next test pass during the current GREEN | skip scenario tests | skip granular tests when edge cases exist | continue with failing tests | commit code.

"Just this once" → STOP. Restart from RED.

---

## Integration With openspec-plus-apply

* **Subagent mode:** implementer subagent uses `skill` tool to load this skill before any code; if unavailable, reads this SKILL.md directly from the skills directory. Each subagent loads fresh in isolated context.
* **Inline mode:** main agent uses `skill` tool once at Phase 2 start; if unavailable, reads this SKILL.md directly from the skills directory. If already loaded in main agent context, reference instead of reload.

The slice's pre-mark gate (lint + format + tests + other on affected files) runs AFTER the TDD cycle completes for all tests AND every Gherkin scenario is covered. Gate failure → return to failing test's TDD cycle. Never bypass.

---

## Success Criteria

**Succeeds:** Pre-RED done, every Gherkin scenario has a passing test, all discovered tests pass, no untested branches/edge cases remain, every test observed to fail before passing, full per-test cycle completed before starting next, REFACTOR assessed and recorded for each, minimal production code (only current test's code path per GREEN), pristine output, no unnecessary comments, no adjacent refactoring, gate clean.

**Fails:** Pre-RED skipped, batching, next test before current REFACTOR complete, REFACTOR assessment skipped, production code without red-then-green test, production code covering future tests in current GREEN, tests skipped/commented, comments on obvious code, adjacent code touched, speculative abstractions, scenario paraphrased instead of translated, uncovered scenarios, untested branches/edge cases ignored.
