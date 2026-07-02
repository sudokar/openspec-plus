# Final Whole-Change Reviewer Prompt Template

Dispatch the whole-change reviewer subagent at Phase 3 (after all slices `[x]`, before final cumulative gate).

**Purpose:** Review entire change end-to-end as one diff — verify cross-slice integration, surface design issues revealed by implementation, apply implementation principles at change scope.

**Out of scope:** spec/design alignment (that's `openspec-verify-change`). **Reviewer reads everything itself** — controller passes artifact paths and all changed file paths; reviewer opens artifacts and runs `git diff HEAD`, falling back to reading files directly if git unavailable.

---

```
Dispatch subagent of type general (use your subagent/task tool):
  description: "Final whole-change review for {CHANGE_NAME}"
  prompt: |
    You are reviewing the complete implementation of an OpenSpec change
    end-to-end. Each slice has already been individually approved by
    spec-compliance and code-quality reviewers. Your job is to verify
    that slices fit together as one coherent change.

    ## Change

    Name: {CHANGE_NAME}
    Schema: {SCHEMA_NAME}

    ## Artifacts

    Open and read completely (paths only — controller has NOT pre-read
    them for you; you read them in this isolated subagent context):

    * Proposal: {PROPOSAL_PATH}
    * Spec(s): {SPEC_PATHS}
    * Design: {DESIGN_PATH}
    * Tasks (all `[x]`): {TASKS_PATH}

    ## Diff

    Changed files (all slices combined): {CHANGED_FILE_PATHS}

    Run `git diff HEAD -- {CHANGED_FILE_PATHS}` yourself. Controller
    has NOT pre-read the diff. Read the entire diff — cumulative
    output of every slice. If git is unavailable, read each file
    directly and note the fallback in your report.

    ## What To Check

    ### Cross-Slice Integration & Refactoring

    Check across ALL slices combined:

    * Shared interfaces consistent (signatures, types, error
      contracts match between producer/consumer slices)
    * Naming consistent across slices (same concept uses the
      same name)
    * Duplicate logic across slices MUST be consolidated
    * Missed shared abstractions — repeated patterns that should
      be a single shared component. Flag them.
    * Superseded code — earlier slice code made redundant by
      later slice but not removed. Flag it.

    ### Implementation Principles At Change Scope

    Apply at WHOLE change scope — catch issues invisible to
    per-slice reviews:

    1. **Surgical** — no cross-slice scope creep; every slice's
       work traces to a task.
    2. **Simplicity** — no emergent abstractions, config, or
       error handling not required by individual slice tasks.
    3. **Think First** — cross-slice integration choices are
       explicit and intentional, not silently assumed.

    ### Implementation-Reveals-Artifact-Gap

    Surface gaps visible only in implementation — missing scenarios,
    unspecified interfaces, emergent components, task boundary
    ambiguities. Flag which artifact needs updating (proposal,
    spec, design, or tasks).

    ## Calibration

    Severity:

    * **Critical** — broken integration, regression introduced by
      combination of slices. Must fix.
    * **Important** — cross-slice naming drift, redundant abstractions,
      cumulative scope creep. Should fix.
    * **Minor** — observation for future work.

    Artifact gaps are NOT severity-categorized — advisory only,
    NOT code-fix items.

    ## Return Format

    **Strengths**
    - <cross-slice strengths backed by paths or commit refs>

    **Issues**

    Critical:
    - <issue> — file:line — <why it matters at change scope>
    - ...

    Important:
    - <issue> — file:line — <why it matters at change scope>
    - ...

    Minor:
    - <issue> — file:line — <observation>
    - ...

    **Artifact Gaps (advisory)**
    - <observation> — <artifact to update: proposal, spec, design, or tasks>
    - ...

    **Assessment**

    <One sentence: ready for final gate | needs Critical/Important
    fixes | escalate (artifact updates required)>
```

---

## Cap And Escalation (controller side)

| Cycle | Action |
|---|---|
| 1-2 | Fix Critical + Important issues. Small/local: fix inline; large/cross-slice: re-dispatch implementer for affected slices. Re-dispatch this reviewer. |
| 3 | STOP. Pause and exit. Cross-slice integration problems warrant artifact-level attention. |

After this reviewer ✅, run the final cumulative gate (lint + format + tests + other on ALL files affected by ALL slices). Gate fails after 3 fix cycles → pause and exit.
