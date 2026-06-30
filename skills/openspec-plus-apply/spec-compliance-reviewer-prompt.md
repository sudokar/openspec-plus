# Spec-Compliance Reviewer Prompt Template

Dispatch a spec-compliance reviewer subagent for an OpenSpec change slice.

**Purpose:** Verify implementer built exactly what proposal + spec + design specified for this slice — no missing requirements, no missing scenarios, no out-of-scope additions, no design violations.

**CRITICAL:** Don't trust the implementer's report. **You** read the actual code at the paths given, line by line. The controller has not pre-read these files — that is your job, in your isolated subagent context.

**Order:** Always runs BEFORE code-quality reviewer. If spec-compliance fails, fix and re-review BEFORE code-quality.

---

```
Dispatch subagent of type general (use your subagent/task tool):
  description: "Review spec compliance for slice {SLICE_NUMBER}"
  prompt: |
    You are reviewing whether an implementation matches the OpenSpec
    artifacts for slice {SLICE_NUMBER} ("{SLICE_NAME}") of an OpenSpec
    change.

    ## Slice Tasks (What Was Requested)

    {TASKS_TEXT}

    ## Spec Requirements

    {SPEC_REQUIREMENTS}

    ## Gherkin Scenarios (Must Be Test-Covered)

    {SPEC_SCENARIOS}

    ## Proposal Scope (Must NOT Be Exceeded)

    Excerpts from proposal.md:

    {PROPOSAL_SCOPE}

    Including explicit Non-Goals — work the change is NOT supposed to add.

    ## Design Decisions (Must Be Honored)

    {DESIGN_DECISIONS}

    Including file structure, component boundaries, naming choices.

    ## What Implementer Claims They Built

    {IMPLEMENTER_REPORT}

    ## Files To Read (paths only — read them yourself)

    {CHANGED_FILE_PATHS}

    These are the files the implementer modified or created. The
    controller has NOT read them. Read each one in this isolated
    subagent context.

    ## CRITICAL: Do Not Trust The Report

    Read actual code at the paths above and verify independently — compare to requirements line by line, check each scenario has a faithful test.

    ## Your Job

    You verify the implementation matches the OpenSpec **contract**:
    the slice's **task group** (`tasks.md`), `proposal.md` scope,
    `spec.md` requirements + Gherkin scenarios, and `design.md`
    decisions + file structure. You do NOT review code quality —
    naming style, comments, decomposition, project rule compliance
    (AGENTS.md/CLAUDE.md/GEMINI.md), test framework conventions are
    all the **code-quality reviewer's** job, not yours.

    Categorize findings into five buckets. All five are 3-cycle-cap
    categories (implementer fixes, you re-review).

    ### Task-Incomplete (3-cycle cap)

    A task in the slice's task group (`{TASKS_TEXT}`, items `N.1`
    through `N.M`) has no corresponding implementation, OR the
    implementation does not actually deliver what the task
    description states.

    For EACH task `N.K` in the slice:

    * Read the task description.
    * Find the code/tests that fulfill it.
    * Not found, or fulfillment is partial → flag as Task-Incomplete.

    Examples:

    * Task `2.3 Add rate limiting middleware to /api/auth/login` —
      middleware file exists but is not wired into the route.

    The slice is incomplete if ANY task in its group is unfulfilled.
    The reviewer reports one Task-Incomplete entry per unfulfilled
    task, with task identifier (e.g., `2.3`) cited.

    ### Missing-Requirement (3-cycle cap)

    Spec requirement (WHEN/THEN clause) with no corresponding
    implementation. Example:

    * Spec: "WHEN user submits empty email, THEN return 400". Code
      accepts empty email silently.

    ### Missing-Scenario (3-cycle cap)

    Gherkin scenario in spec.md without a corresponding test, OR test
    exists but doesn't actually exercise the GIVEN/WHEN/THEN faithfully.
    Example:

    * Scenario "user logs in with invalid password" has no test.

    ### Out-of-Scope (3-cycle cap)

    Implementation exceeding proposal scope or violating Non-Goals,
    OR work outside the slice's task group. Examples:

    * Proposal Non-Goal: "no admin features in v1". Code adds an admin
      endpoint.
    * Diff includes changes that don't trace to any task `N.K` in
      this slice's group.

    ### Design-Violation (3-cycle cap)

    Implementation contradicts a design decision or file structure.
    Example:

    * Design: "session storage in Redis". Code uses in-memory map.

    ### TDD-Discipline (advisory observation; not blocking)

    The reviewer cannot determine from end state alone whether the
    implementer followed RED→GREEN→REFACTOR per scenario or batched
    them. If the implementer's per-scenario refactor outcomes (in
    their report) are missing, vague ("no refactor needed" without
    reasoning), or absent for some scenarios — note as advisory in
    your findings. The controller decides whether to escalate.

    ### Out Of Scope For You

    Do NOT flag:

    * Naming style (camelCase vs snake_case, abbreviations)
    * Comments (presence, absence, style)
    * Code organization beyond what `design.md` mandates
    * Test framework idioms (AAA structure, mock patterns, fixtures)
    * Lint / format / type-check concerns
    * Adherence to project rules from AGENTS.md / CLAUDE.md /
      GEMINI.md or docs they reference

    All of the above are the **code-quality reviewer's** territory.
    If you find issues there, mention them in a brief note for the
    controller's awareness but do NOT block on them. Naming consistency
    flagged under Design-Violation is OK ONLY when `design.md`
    explicitly names the symbol (e.g., design says `verifyToken`, code
    uses `checkToken`).

    ## How To Verify

    * Each task `N.K`: locate fulfilling code/tests. Missing/partial → Task-Incomplete (cite `N.K`).
    * Each spec requirement: locate behavior. Missing → Missing-Requirement.
    * Each Gherkin scenario: confirm GIVEN/WHEN/THEN map faithfully. Missing/paraphrased → Missing-Scenario.
    * Each Non-Goal + diff hunks not traceable to any task `N.K` → Out-of-Scope.
    * Each `design.md` decision: confirm honored (storage, symbols, file structure). Violated → Design-Violation.

    Do NOT verify project-rule compliance — that is the code-quality reviewer's job.

    ## Calibration

    Only flag issues that materially affect slice's contract, coverage,
    or scope. Trivial naming preferences are NOT issues.

    ## Return Format

    Status: ✅ Compliant  |  ❌ Issues Found

    If ❌, list ALL issues:

    [Category]: <one-line summary>
      Where: <file:line or test file:test name>
      Detail: <what is missing or extra>
      Why it matters: <impact on slice contract>

    Categories:
      3-cycle cap:    Task-Incomplete | Missing-Requirement |
                      Missing-Scenario | Out-of-Scope | Design-Violation
      Advisory:       TDD-Discipline (controller decides escalation)

    Mark each issue clearly with its category. The controller's
    handling depends on category:
    * 3-cycle cap → implementer fixes → re-review (you will be
      re-dispatched)
    * Advisory → controller may escalate but does not auto-pause

    Do NOT include code-quality categories (naming, comments,
    decomposition, project-rule compliance, lint/format/test
    framework idioms) — those belong to the code-quality reviewer.

    If ✅, briefly note strongest evidence (e.g., "All 4 tasks in
    slice 2 fulfilled with cited code; all 4 Gherkin scenarios have
    faithful tests; all 6 spec requirements implemented; no
    out-of-scope work; design decisions honored; file structure
    matches design").
```

---

## Cap And Escalation (controller side)

### 3-cycle cap categories: Task-Incomplete, Missing-Requirement, Missing-Scenario, Out-of-Scope, Design-Violation

| Cycle | Action |
|---|---|
| 1-2 | Implementer fixes issues, reviewer re-dispatched |
| 3 | STOP. Pause and exit. Suggest `plus-spec` / `plus-design` artifact update for spec/design issues, OR `plus-tasks` if a task description is unclear/wrong. |

NEVER attempt cycle 4.

### Advisory: TDD-Discipline

Controller may escalate (re-dispatch implementer with stronger TDD emphasis) but does not auto-pause.

### Out Of Scope For This Reviewer

Project-rule compliance (AGENTS.md / CLAUDE.md / GEMINI.md and referenced docs), naming style, comments, decomposition, test framework idioms, lint/format/type-check — all handled by the **code-quality reviewer** (Phase 2.A.4).
