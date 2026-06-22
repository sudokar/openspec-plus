# Final Whole-Change Reviewer Prompt Template

Dispatch the whole-change reviewer subagent at Phase 3 (after all slices `[x]`, before final cumulative gate).

**Purpose:** Review entire change end-to-end as one diff — verify cross-slice integration, surface design issues revealed by implementation, apply implementation principles at change scope.

**Out of scope:** spec/design alignment (that's `openspec-verify-change`, run manually by user). This reviewer focuses on whether slices fit together as one coherent change.

**Reviewer reads everything itself.** The controller passes artifact paths and `BASE_SHA` / `HEAD_SHA`. The reviewer opens the artifacts and runs `git diff` in its isolated subagent context. The controller has NOT pre-read the diff or the artifacts beyond what vanilla already loaded.

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

    ## Diff Range

    BASE_SHA: {BASE_SHA}
    HEAD_SHA: {HEAD_SHA}

    Run `git diff {BASE_SHA} {HEAD_SHA}` yourself. The controller has
    NOT pre-read the diff. Read the entire diff. This is the cumulative
    output of every slice.

    ## What To Check

    ### Cross-Slice Integration

    * Shared interfaces consistent (signatures, types, error contracts match between producer/consumer)
    * Naming uniform across slices (no drift)
    * Cohesive file structure matches `design.md`
    * No duplicate utilities across slices

    ### Implementation Principles At Change Scope

    Apply at WHOLE change, not individual slices:

    1. **Surgical** — every line traces to task/requirement; no cross-slice scope creep
    2. **Simplicity** — no emergent abstractions/config/error handling beyond what slices require
    3. **Goal-Driven** — every Gherkin scenario has a passing test
    4. **Think First** — cross-slice integration choices surfaced, not silently assumed

    ### Implementation-Reveals-Design-Issue

    Surface design gaps that became visible only through implementation (e.g., load-bearing cross-slice interfaces unspecified in design, failure modes handled ad hoc, emergent components needing explicit ownership). These warrant `plus-design` updates.

    ### Test Coverage

    Spot-check cumulative: every requirement tested, every scenario faithful, no skipped/commented tests.

    ## Calibration

    Severity:

    * **Critical** — broken integration, missing scenario coverage,
      regression introduced by combination of slices. Must fix.
    * **Important** — cross-slice naming drift, redundant abstractions,
      cumulative scope creep. Should fix.
    * **Minor** — observation for future work.

    Implementation-reveals-design-issues are NOT severity-categorized
    — surfaced separately as recommendations for artifact updates,
    NOT as code-fix items.

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

    **Implementation-Reveals-Design-Issue (advisory)**
    - <observation> — <which slices touched it> — <suggested artifact
      update: spec, design, or both>
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

After this reviewer ✅, run the final cumulative gate (lint + format + tests + other on ALL files affected by ALL slices). Gate fails after 3 fix cycles → also pause and exit.

Implementation-reveals-design-issue findings are advisory — surface in hand-back output but don't block. User decides whether to update artifacts before `/opsx-archive` or accept the gap.
