# Code-Quality Reviewer Prompt Template

Dispatch a code-quality reviewer subagent for an OpenSpec change slice.

**Purpose:** Verify implementation is well-built — clean, surgical, simple, tested, maintainable.

**Order:** Dispatch ONLY after spec-compliance reviewer returns ✅. Never run code-quality before spec-compliance is clean.

**Reviewer reads the diff itself.** Controller passes changed file paths; reviewer runs `git diff HEAD` in its isolated context, falling back to reading files directly if git unavailable.

---

```
Dispatch subagent of type general (use your subagent/task tool):
  description: "Review code quality for slice {SLICE_NUMBER}"
  prompt: |
    You are reviewing code quality of slice {SLICE_NUMBER}
    ("{SLICE_NAME}") of an OpenSpec change. Spec compliance has already
    been confirmed; do not re-review correctness against the spec.

    ## Description

    {SLICE_SUMMARY_FROM_IMPLEMENTER}

    ## Plan / Requirements

    Slice tasks: {TASKS_TEXT}

    Relevant spec requirements: {SPEC_REQUIREMENTS}

    ## Diff

    Changed files: {CHANGED_FILE_PATHS}

    Run `git diff HEAD -- {CHANGED_FILE_PATHS}` yourself. Controller
    has NOT pre-read the diff. Read carefully; every claim must
    reference actual code. If git is unavailable, read each file
    directly and note the fallback in your report.

    ## Project Standards Documents (Read Independently)

    The project's source of truth for code style, file organization, naming, testing conventions, lint/format/test commands, and framework idioms.

    {STANDARDS_DOC_PATHS}

    Open and read each one, including referenced docs (e.g., `AGENTS.md` links to `docs/coding-standards.md` — read that too).

    The implementer was required to follow every documented rule strictly. Verify the implementation honors every applicable rule.

    ## What To Check

    Standard concerns:

    * **File responsibility** — each file one clear responsibility,
      well-defined interface
    * **Decomposition** — each unit understandable and testable
      independently
    * **File growth** — did this slice create new files already large,
      or significantly grow existing files? Don't flag pre-existing
      sizes — focus on what THIS slice contributed
    * **Naming** — match what things do, not how; consistent with
      project's naming conventions
    * **Error handling** — matches codebase's existing patterns
    * **Test quality** — tests exercise real behavior not mock
      behavior; comprehensive for slice's scenarios

    Implementation principles concerns:

    * **Surgical Changes** — every changed line traces to a slice task? Adjacent code refactored without being asked?
    * **Simplicity First** — abstractions for single-use code? Configurability slice didn't request? Would a senior engineer call this overcomplicated?
    * **No Speculative Features** — anything beyond what slice tasks and Gherkin scenarios required?

    Code-style concerns:

    * **Comments** — comments on non-complex logic are noise; flag them. If a comment exists, ask: could a better name/structure remove it? If yes, flag as Important (refactor-first rule).
    * **Testability** — hard to test = hard to use, usually a sign of coupling.
    * **Readability** — does each function fit one mental load?
    * **Maintainability** — would a new contributor understand the slice's code shape in a few minutes?
    * **No commented-out code, no TODO, no FIXME, no "explained later" markers.**

    Project standards compliance (MANDATORY):

    Read every rule in the project standards docs. Verify the implementation honors each applicable rule — naming, structure, framework idioms, testing conventions, type annotations, file placement. Cherry-picking is itself a violation. Cite source doc for each violation (e.g., "AGENTS.md §Naming: `usrCtrl` should be `userController`").

    Severity: **Important** for most violations; **Critical** only when the rule breaks the build; **Minor** for "should"-level stylistic rules.

    ## Calibration

    Severity:

    * **Critical** — bug, broken behavior, security issue, regression,
      rule violation that breaks the project.
    * **Important** — design concern, scope violation (surgical/
      simplicity), unclear naming, missing test coverage for a real
      edge case, project-rule violation (naming/structure/framework
      idioms/testing conventions).
    * **Minor** — style preference, suggestion, observation,
      "should"-level rule violation. Note but don't block.

    Don't invent issues. Slice clean → say so. Cite the source doc
    for every project-rule violation.

    ## Return Format

    **Strengths**
    - <brief, specific strengths backed by file:line>

    **Issues**

    Critical:
    - <issue> — file:line — <why it matters>
    - ...

    Important:
    - <issue> — file:line — <why it matters>
    - ...

    Minor:
    - <issue> — file:line — <observation>
    - ...

    **Assessment**

    <One sentence: ready to proceed | needs Critical/Important fixes>
```

---

## Cap And Escalation (controller side)

| Cycle | Action |
|---|---|
| 1-2 | Implementer fixes Critical + Important, reviewer re-dispatched |
| 3 | STOP. Pause and exit. Architecture is wrong, not implementation. Suggest `plus-design` artifact update. |

NEVER attempt cycle 4. Minor issues are noted but never block progression.
