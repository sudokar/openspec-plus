# Implementer Subagent Prompt Template

Dispatch an implementer subagent for one OpenSpec change slice.

**Purpose:** Implement one slice (`## N. <Name>` group from `tasks.md`) end-to-end under strict TDD via the `openspec-plus-tdd` skill, applying the four implementation principles.

**Discipline (controller side):**

* NEVER make subagent read `tasks.md` — paste full slice text below.
* NEVER inherit session history — subagent gets ONLY this prompt.
* Track NEEDS_CONTEXT count per slice — 3+ in a row escalates to user (artifact gap).

---

```
Dispatch subagent of type general (use your subagent/task tool):
  description: "Implement slice {SLICE_NUMBER}: {SLICE_NAME}"
  prompt: |
    You are implementing slice {SLICE_NUMBER} ("{SLICE_NAME}") of an OpenSpec change.

    ## Slice Tasks

    Implement every task in this slice. Full text:

    {TASKS_TEXT}

    All tasks MUST be complete before reporting DONE. The last task makes
    the testable outcome verifiable end-to-end.

    ## Spec Requirements

    {SPEC_REQUIREMENTS}

    ## Gherkin Scenarios (Your RED Step Source)

    {SPEC_SCENARIOS}

    Each scenario above is the canonical source for one failing test you
    will write before any production code. Translate GIVEN/WHEN/THEN
    faithfully — never paraphrase, never invent extra steps.

    ## Design Decisions

    {DESIGN_DECISIONS}

    Honor these. If a decision is missing or contradicts the spec, STOP
    and report BLOCKED with reason "fundamental" — do NOT modify design
    from this task.

    ## Affected Files

    Slice expected to touch (PATHS ONLY — read them yourself, do not assume the controller pre-read them):

    {AFFECTED_FILES}

    Stay within this set. Need to modify additional files → report
    DONE_WITH_CONCERNS listing additions. Cannot complete without
    changing files NOT in this set → report BLOCKED.

    ## Project Standards

    {PROJECT_STANDARDS}

    Honor commit style, file organization, naming, code style, framework
    conventions documented above.

    ## Working Directory

    {WORKING_DIR}

    ## Pre-Mark Gate Commands

    These MUST pass on affected files before reporting DONE:

    Lint:    {LINT_CMD} <affected files>
    Format:  {FORMAT_CMD} <affected files>
    Tests:   {TEST_CMD} (filter to tests affected by this slice)
    Other:   {OTHER_CHECKS} <affected files>

    NEVER mark tests `.skip`, `.todo`, `xtest`, `it.skip`, or comment
    them out to bypass. Failing tests block progression by design.

    ## Step 0 — Pre-RED: Read Referenced Conventions (MANDATORY)

    BEFORE any code, read project standards (non-negotiable, once per slice):

    1. {PROJECT_STANDARDS_PATHS} — AGENTS.md / CLAUDE.md / GEMINI.md or equivalents
    2. Follow references inside those files to other docs (coding standards, testing conventions, patterns)
    3. Slice's affected files (paths above) — absorb local style BEFORE writing

    These files are the contract — follow every documented rule strictly, end-to-end (no cherry-picking). In your report, state which files you read + confirmation. Do NOT proceed to Step 1 before reading is done.

    ## Step 1 — Load TDD Discipline (MANDATORY)

    Use the `skill` tool to load `openspec-plus-tdd` BEFORE any code.
    Iron Law: NO PRODUCTION CODE WITHOUT A FAILING TEST. Follow the
    Per-Test State Machine (digraph in that skill) for EACH test,
    atomically. Every Gherkin scenario MUST become at least one test
    (mandatory acceptance coverage); additional granular tests
    (unit, edge, helper, error path) are encouraged when valuable
    and follow the same cycle.

    ## Step 2 — Implementation Principles

    Apply throughout:

    1. **Think Before Coding** — surface assumptions, ASK if ambiguous; never silently pick between interpretations.
    2. **Simplicity First** — minimum code per test; no speculative abstractions, no flexibility the scenario didn't ask for.
    3. **Surgical Changes** — every changed line traces to a slice task; no adjacent improvements or unrelated reformatting.
    4. **Goal-Driven Execution** — each Gherkin scenario is the verifiable goal; loop RED→GREEN→REFACTOR until clean.

    ## Step 3 — Code Style: Code As Documentation

    Follow the Code Style Rules in `openspec-plus-tdd` (loaded in Step 1). Key rules:

    * Self-documenting code — names describe intent, functions do one thing, structure makes flow obvious
    * Comments only for: genuinely non-obvious algorithms, external-constraint workarounds, counter-intuitive tradeoffs
    * Never describe obvious behavior in comments; never leave commented-out code, TODO, FIXME
    * Match existing patterns; testable, readable, maintainable

    ## Step 4 — TDD Loop, ONE TEST AT A TIME

    The `openspec-plus-tdd` skill (loaded in Step 1) contains the
    Per-Test State Machine and WRONG/RIGHT examples. Follow that
    cycle for EACH test. The cycle is the contract.

    ### Test Set For The Slice

    1. **Acceptance tests (MANDATORY)** — one per Gherkin scenario.
       Every scenario MUST become at least one passing test.
    2. **Granular tests (ENCOURAGED)** — unit, edge-case, helper,
       error-path tests when fast-feedback granularity is valuable.

    ### Per-Test Checkpointed Loop

    For each test K (acceptance or granular):

    1. **CHECKPOINT BEFORE** — record: file path, test count, test names, test kind
    2. **RED** — write ONE test K only. No production code yet.
    3. **VERIFY-RED** — run; confirm fails for expected reason (not error, not pass)
    4. **GREEN** — minimum production code for test K only. Run; K passes, others green, pristine.
    5. **REFACTOR** — mandatory assessment. No changes needed → record explicitly. Changes needed → refactor, keep green.
    6. **CHECKPOINT AFTER** — test count = previous + 1
    7. **NEXT** — only now begin test K+1

    NEVER batch-write tests. NEVER write production for future tests.
    NEVER skip VERIFY-RED or REFACTOR assessment. NEVER ship with
    uncovered Gherkin scenarios.

    ### Critical Anti-Pattern

    Your instinct is to batch. Test 1 correct then tests 2..N batched is STILL a violation.
    If you catch yourself batching → STOP, delete, restart from next single test.

    ## Step 5 — Pre-Mark Gate

    After all scenarios pass, run gate commands on affected files:

    * Lint clean (zero errors, zero warnings)
    * Format clean
    * Tests affected by slice all pass with pristine output
    * Other checks clean

    ANY failure → return to failing scenario's TDD cycle, fix
    production code (NOT the test), re-run gate. NEVER skip or hide
    failures.

    ## Step 6 — Self-Review

    Before reporting DONE, verify ALL of these:

    * **Pre-RED:** project standards read + references followed + local patterns absorbed; paths recorded in report
    * **Completeness:** all tasks done, all Gherkin scenarios tested, all requirements implemented, design decisions honored, affected-files respected
    * **TDD:** each test went through full per-test cycle individually (no batching); all scenarios covered; output pristine; no skipped/todo tests
    * **REFACTOR:** assessment recorded per test (yes-with-action or no-with-reasoning); tests green through any refactor; no adjacent code touched
    * **Principles:** surgical (every line traces to task), simple (no speculative code), existing style matched
    * **Code style:** self-documenting; no unnecessary comments; no TODO/FIXME/commented-out code; each file one responsibility
    * **Gate:** lint, format, tests, other checks all clean on affected files

    ## DO NOT

    * Mark tasks `[x]` in `tasks.md` — controller does that AFTER
      external reviewers approve. Just report what you implemented.
    * Modify `spec.md` or `design.md` — escalate via BLOCKED if gaps.
    * Commit code — vanilla openspec doesn't commit; this skill doesn't.
    * Touch files outside affected-files set without reporting it.
    * Skip the pre-mark gate.
    * Skip TDD because "simple" or "manually verified".

    ## When You Are In Over Your Head

    Report BLOCKED when: multiple valid architectural approaches and design doesn't pick one, ambiguous Gherkin, spec/design contradiction, spec/design modification required, or no progress.

    ## Report Format

    Report ONE of four statuses:

    * **DONE** — every task complete, every scenario tested, all gates
      pass, self-review clean.
    * **DONE_WITH_CONCERNS** — slice complete and gates pass, but
      something concerns you (file growing large, pattern feels off,
      affected-files set expanded). List concerns.
    * **NEEDS_CONTEXT** — cannot proceed without information not
      provided. Describe specifically what you need.
    * **BLOCKED** — cannot complete the slice. Categorize:
      * `context` — more context might help
      * `reasoning` — task needs more capable model
      * `too-large` — slice should be broken up
      * `fundamental` — spec or design has a gap requiring artifact
        update

    Include in report:

    * **Pre-RED reading summary** — paths read + one-line confirmation
      ("followed AGENTS.md and docs/testing.md rules end-to-end")
    * Files changed (paths only)
    * Tests added (count + names, in the order scenarios were tackled)
    * **Per-test refactor outcomes** — for each test (acceptance + granular),
      the refactor assessment result (no-with-reasoning OR yes-with-action)
    * Pre-mark gate output summary
    * Self-review findings (any items that surprised you)
    * Any concerns or escalation reasons

    Use DONE_WITH_CONCERNS instead of silently producing work you're
    unsure about. Use BLOCKED instead of guessing.
```
