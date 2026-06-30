---
name: openspec-plus-tasks
description: "MANDATORY skill that activates whenever the OpenSpec tasks phase begins. Triggers: /opsx-new, /opsx-ff, or /opsx-continue runs; openspec-new-change, openspec-ff-change, openspec-continue-change, or openspec-explore is active; `openspec instructions tasks` is invoked; or the user wants to create, update, review, refine, or discuss an OpenSpec tasks.md file."
version: 1.1.0
priority: high
disable-user-invocation: true
---

# OpenSpec Plus Tasks

## Mission

Enrich OpenSpec's tasks.md generation with vertical-slice discipline and subagent review. Format is owned by the schema template (resolved in Phase 0) — this skill provides thinking discipline before the file is written and a subagent review after.

Two anchors that shape every task group:

1. **Each task group = a vertical slice** — end-to-end through whatever layers are needed, not horizontal layer-by-layer
2. **Each task group completion = a testable outcome** — after the last task in a group is checked, the user can verify something works

---

> **This skill is RIGID. NEVER write tasks.md before completing Phase 1 vertical-slice discovery. NEVER skip the subagent review. Reading source code, writing implementation code, running TDD, including exact commit messages, or restructuring the template format is a skill violation.**

**Red flags — STOP, you are about to violate this skill:**

- "I'll group by layer (DB / API / UI) to keep similar tech together"
- "This task group is preparatory work, it doesn't need to be testable on its own"
- "I'll add a 'Files' or 'Dependencies' section to the tasks.md"
- "I should peek at the code to figure out what files to touch"
- "Let me put exact code or shell commands in the task description"
- "The reviewer subagent isn't needed, the tasks look fine to me"
- "Reviewer flagged minor issues, I'll re-dispatch after fixing to confirm"
- "I'll add commit instructions per task"
- "Let me write a TDD-ordered set of sub-tasks"

---

## Inputs

* **Proposal** — intent, scope boundaries, non-goals
* **Specs** — requirements with Gherkin scenarios
* **Design** — architecture decisions, file structure, integration points
* **Existing tasks.md files** in `openspec/changes/` — for naming convention only
* **NEVER read source code** — design phase already grounded patterns

If the design is missing detail you need to slice the work, the design is incomplete. Surface the gap to the user — do not paper over with code reading.

---

## Fast-Forward Mode (opsx-ff)

When invoked from `/opsx-ff` (or user requests fast-forward / "keep momentum" / "skip questions"):

* Skip clarifying questions about slice boundaries. Identify vertical slices and testable outcomes from spec and design directly.
* Where slicing is ambiguous, make a reasonable choice and document the rationale in the task descriptions.
* Run vertical-slice identification and dependency ordering NORMALLY.
* Run Phase 2.2 reviewer subagent NORMALLY — single-shot review still applies. Subagent catches slicing or coverage issues that fast-mode assumptions miss.

Pause only if reviewer surfaces material issues, or slicing is critically ambiguous in a way that affects multiple groups.

Detection: `/opsx-ff` in conversation OR explicit FF keywords ("fast-forward", "skip questions", "keep momentum", "just create everything", "all artifacts", "no questions"). Normal continuation words ("continue", "go", "proceed", "next", "yes", "ok") do NOT trigger FF — requires explicit intent.

---

## Workflow

Three phases. NEVER skip or merge.

```text
Phase 0: Schema & Template Resolution
Phase 1: Vertical Slicing & Discovery
Phase 2: Write & Artifact Compliance Review
```

---

## Workflow Visibility (MANDATORY)

Display workflow phases via task tool at start; update as each phase completes.

---

## Core Principles

- **Vertical Slice Per Group** — each `## N.` heading is one vertical slice (feature/capability that produces a testable end-to-end outcome), NOT a layer (DB/API/UI), NOT a phase (setup/build/polish), NOT a category (tests/docs/config).
- **Testable Outcome Per Group** — after the last `- [ ]` in a group is checked, the user can verify something works in one sentence; if not, merge with the slice that completes the outcome or surface as a design gap.
- **Verifiable Tasks** — each task small enough that "done" is unambiguous; no "implement appropriately", "handle edge cases", "make it work".
- **Dependency Order Across Slices** — order so each slice's prerequisites are satisfied by earlier slices; within-slice dependencies managed by task ordering inside the group.
- **YAGNI — No Speculative Tasks** — every task traces to a spec requirement, design decision, or stakeholder need.
- **Respect Project Standards** — read project instruction files (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, equivalents) at Phase 1 start; tasks MUST follow project conventions (paths, naming, structure); conflicts → surface to user, never silently override.
- **Implementation Discipline Belongs Elsewhere** — this skill produces a clean, sliced, verifiable task list; nothing more.

---

## Phase 0: Schema & Template Resolution

Run FIRST, before any slicing:

```bash
openspec instructions tasks --change <name> --json
```

Extract from response:
- `template` — structural authority; format you MUST follow
- `instruction` — guidance on what content each section needs
- `rules` — project constraints to honor

Parse template structure. If the template includes metadata fields (priority, effort, labels, categories) beyond the default `## N. Name` + `- [ ] N.M desc` format, Phase 1 must collect that information during slicing. Discovery questions MUST satisfy template requirements.

---

## Phase 1: Pre-Write Discovery & Vertical Slicing (MANDATORY)

**Pre-existing answers:** If recent conversation already identifies vertical slices, names testable outcomes, or establishes dependency ordering — via prior exploration, a detailed initial request, or any other source — incorporate those and SKIP redundant clarification. Ask ONE question at a time only for unresolved slicing decisions. NEVER re-ask what's already established.

### 1. Read Inputs

Read in this order:

1. **Project-level instruction files first** — `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, equivalents at project root, `.claude/`, `.opencode/`, `docs/`. These capture conventions for commit style, branching, file organization, naming. Tasks MUST follow these.
2. Proposal, specs, design.
3. Existing tasks.md files in `openspec/changes/` for naming convention only.

NEVER read source code. If grounding is missing, the design is incomplete — surface that to the user.

### 2. Identify Vertical Slices

From spec requirements and design components, identify slices that deliver testable outcomes. Each slice spans whatever layers (DB, API, UI, CLI, background jobs) are needed.

For each candidate slice ask: "When all tasks in this group are done, what can the user observe or verify?" If "nothing observable", it's not a slice — re-slice or merge.

### 3. Name The Testable Outcome For Each Slice

For each slice, write the testable outcome in one plain sentence. Example: "User can log in with email/password and see the dashboard".

Cannot name in one sentence? The slice is wrong — re-slice or surface a design gap.

### 4. Order Slices By Dependency

Some slices depend on others (e.g., "user signup" before "user login"). Order so prerequisites are satisfied by earlier slices.

### 5. Clarifying Questions

If slicing is ambiguous, use **question tool**, ONE question at a time:

* "Which capability should be the first vertical slice?"
* "Is X part of slice A, or its own slice?"

NEVER batch. NEVER assume.

### 6. Rules Compliance Check

Review `rules` from Phase 0. If any rule constrains task breakdown, verify the slices honor those constraints. If a rule is violated, surface the conflict to the user before proceeding.

Mark Phase 1 complete. Update task status. Proceed to Phase 2.

---

## Phase 2: Write & Artifact Compliance Review

### 2.1 Write tasks.md

Use the `template` and `outputPath` from Phase 0. Use the structure exactly. Do not restructure, add sections, or change numbering convention.

**Before writing — 2 mandatory steps:**

**Step 1 — Map Phase 1 slices to template:** Phase 1 slices and task descriptions are in context — use them directly. Do NOT extract, summarize, or rephrase. Map each slice and its tasks to the corresponding `## N.` group. Nothing left unmapped.

**Step 2 — Density check:** Tasks must be at least as dense as Phase 1. Significantly shorter = information loss.

Write from the mapping. Do NOT discard any Phase 1 slice or task.

**CRITICAL — Missing or underrepresented information becomes missing or wrong work in implementation.** Every Phase 1 slice must appear with full weight intact.

Following the template from Phase 0, write:

```markdown
## 1. <First Vertical Slice — testable outcome implied by name>

- [ ] 1.1 <Task — references file paths inline where useful>
- [ ] 1.2 <Task — verifiable, small enough for one work session>
- [ ] 1.3 <Final task that completes the testable outcome for this slice>

## 2. <Second Vertical Slice>

- [ ] 2.1 <Task>
- [ ] 2.2 <Task>
```

Rules:

* File paths go inline in task descriptions (e.g., "Add validation to `src/auth/middleware.ts`")
* Each task is verifiable
* Each task small enough for one work session
* Last task in a group makes the testable outcome verifiable
* No code, no shell commands, no commit messages, no expected output

Write to `outputPath` from Phase 0.

### 2.2 Artifact Compliance Review (MANDATORY — single-shot)

Dispatch subagent of type `general` (use your subagent/task tool) with reviewer prompt below. Subagent loads tasks.md plus inputs into its own context, returns structured findings list, exits.

**Discipline:**

* Single-shot — dispatch once, get findings, fix inline in root, surface for user review
* NEVER re-dispatch after fixing
* NEVER skip subagent because "tasks look fine to me"
* NEVER reload tasks.md / proposal / specs / design into root for review — defeats the purpose

#### Reviewer Subagent Prompt

```
You are a tasks.md reviewer for an OpenSpec change. Verify the tasks file is
complete, well-sliced, and ready for user review.

Inputs:
- Tasks file: <TASKS_PATH>
- Proposal file: <PROPOSAL_PATH>
- Specs file(s): <SPECS_PATHS>
- Design file: <DESIGN_PATH>
- Template: <TEMPLATE_CONTENT_FROM_PHASE_0>

Read all inputs before reviewing. Check each category:

| Category | What to look for |
|---|---|
| Vertical slicing | Every `## N.` group is a feature/capability slice — NOT a layer (DB/API/UI), NOT a phase (setup/build/polish), NOT a category (tests/docs/config) |
| Testable outcomes | Each group's last task makes a one-sentence outcome verifiable end-to-end |
| Spec coverage | Every spec requirement maps to at least one task |
| Design coverage | Every design decision or component maps to at least one task |
| Naming consistency | Same component or capability named the same way across all tasks |
| Format compliance | Artifact structure matches the provided template — no custom sections, no extra nesting, no deviations |
| No implementation detail | No code snippets, no exact shell commands, no commit messages, no expected output |
| Placeholder scan | No TBD, TODO, "[fill in]", "implement appropriately", "handle edge cases", "make it work" |
| Verifiability | Each task description tells you when it's done |
| Dependency order | Slices ordered so prerequisites are satisfied by earlier slices |
| Speculative work | No tasks for unrequested features, "while we're at it" additions, or just-in-case work |

Calibration: only flag issues that would mislead implementation, lose
coverage, or break the slicing discipline. Minor wording polish is NOT an issue.

Return format:

Status: Approved | Issues Found

Issues (if any):
- [Category]: [specific finding] — [why it matters]

Recommendations (advisory, do not block):
- [optional improvement suggestions]
```

After receiving reviewer's response:

* Status Approved → mark Phase 2 complete, surface tasks.md for user review
* Status Issues Found → fix each Issue inline in root context, surface for user review (NEVER re-dispatch)

Mark Phase 2 complete. Update task status.

---

## Anti-Patterns

NEVER group tasks by layer (DB / API / UI separately) — vertical slice only. NEVER create groups for setup, build, polish, refactor, or other phase categories. NEVER create groups for "tests", "docs", or "config" — those activities live inside their respective vertical slices.

NEVER write code, exact shell commands, expected output, or commit messages in task descriptions. NEVER add custom sections (Files, Dependencies, Notes, Acceptance) — preserve template format. NEVER include TDD ordering inside a task group — that's the implementor's job. If implementation discipline appears, redirect to the implementor phase.

---

## Success Criteria

**Succeeds:** every group is a vertical slice with testable outcome, every spec/design item covered, tasks verifiable + dependency-ordered + consistently named, template format preserved, reviewer dispatched once with findings applied.

**Fails:** groups are layers/phases/categories, no testable outcome, format diverges from template, code read in root, reviewer skipped/re-dispatched, coverage gaps, speculative tasks, implementation discipline in tasks.md.
