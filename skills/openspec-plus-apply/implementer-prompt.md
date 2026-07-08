# Implementer Subagent Prompt Template

Dispatch an implementer subagent for one OpenSpec change slice.

**Purpose:** Implement one slice end-to-end under strict TDD loop for each test via `openspec-plus-tdd`.

---

> type-general-purpose dispatch: Claude Code `Agent(general-purpose)` · Devin/Windsurf `run_subagent(subagent_general)` · OpenCode `@general` · Codex `spawn_agent` (`multi_agent=true`) · Antigravity `invoke_subagent(self)` · Pi `subagent` · unlisted → self-assess; no dispatch tool → main agent chooses inline.

```
Dispatch subagent of type general-purpose (use your subagent/task tool):
  description: "Implement slice {SLICE_NUMBER}: {SLICE_NAME}"
  prompt: |
    Implement slice {SLICE_NUMBER} ("{SLICE_NAME}") of an OpenSpec change.

    ## Tasks (this slice ONLY)

    {TASKS_TEXT}

    These tasks are your COMPLETE scope. Do NOT look for or implement
    tasks outside this list — no other slices, no adjacent work.

    ## Artifacts (read yourself)

    * Spec(s): {SPEC_PATHS} — read for context, but ONLY implement
      scenarios and requirements that map to the tasks listed above.
      Ignore scenarios belonging to other slices.
    * Design:  {DESIGN_PATH} — read end-to-end, honor all decisions

    ## Affected Files (read yourself)

    {AFFECTED_FILES}

    Stay within this set. Need additional → DONE_WITH_CONCERNS.
    Cannot complete without files outside set → BLOCKED.

    ## Project Standards (read yourself)

    {PROJECT_STANDARDS_PATHS}

    Follow every documented rule. No cherry-picking.

    ## Working Directory

    {WORKING_DIR}

    ## Gate Commands

    Run on affected files before reporting DONE:

    Lint:   {LINT_CMD}
    Format: {FORMAT_CMD}
    Tests:  {TEST_CMD}
    Other:  {OTHER_CHECKS}

    NEVER `.skip`, `.todo`, `xtest`, `it.skip`, or comment out tests.

    ## TDD (MANDATORY)

    Load `openspec-plus-tdd` via skill tool BEFORE any code.
    No skill tool → read its SKILL.md from your skills directory.

    Follow its workflow strictly — the per-test loop IS your
    implementation process. One test per iteration. Gherkin scenarios
    are guiding requirements — write whatever tests fully satisfy them.
    Unit, edge, error tests are mandatory, not optional.

    ## Post-All-TDD-Loops

    After all TDD loops exits:

    1. **Cross-task refactor** — scan ALL code across ALL tasks in
       this slice:
       a. Enumerate applicable clean code principles
       b. Enumerate applicable refactoring patterns
       c. Enumerate applicable code smells
       d. Check against project conventions from Phase 0
       Assess each enumerated item across task boundaries.
       No cherry-picking — list then judge.
       Test after each change. Record outcome per item
       or "nothing found — <reason per item>."

    2. **Gate** — run gate commands on affected files. All clean.
       Failure → fix via TDD loop, re-run gate.

    ## Report

    Status — one of:

    * **DONE** — all tasks complete, all scenarios tested, gate clean.
    * **DONE_WITH_CONCERNS** — complete + gate clean, with concerns listed.
    * **NEEDS_CONTEXT** — cannot proceed. Describe what's needed.
    * **BLOCKED** — cannot complete. Category: `context` | `reasoning` |
      `too-large` | `fundamental`

    Include: files changed, tests added (count + names + order),
    per-test refactor outcomes, gate summary.

    ## DO NOT

    * Implement tasks or scenarios outside the Tasks list above
    * Read or act on tasks.md directly — your tasks are inlined above
    * Mark tasks `[x]` in tasks.md — main agent does that post-review
    * Modify spec.md or design.md — BLOCKED if gaps found
    * Commit code
    * Skip TDD or any part of its loop
```
