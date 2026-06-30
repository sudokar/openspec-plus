---
name: openspec-plus-proposal
description: "MANDATORY skill that activates whenever the OpenSpec proposal phase begins. Triggers: /opsx-new, /opsx-ff, or /opsx-continue runs; openspec-new-change, openspec-ff-change, openspec-continue-change, or openspec-explore is active; `openspec instructions proposal` is invoked; or the user wants to create, update, review, refine, or discuss an OpenSpec proposal."
version: 1.1.0
priority: high
disable-user-invocation: true
---

# OpenSpec Plus Proposal

## Mission

Brainstorming-style interactive discovery for OpenSpec proposals. Produce a lean Why / What Changes / Capabilities / Impact proposal at intent level — never sliding into design, spec, or implementation.

OpenSpec provides the format and template (resolved dynamically in Phase 0). This skill provides the process discipline: five-lens Q&A, decomposition check, scope discipline, subagent review.

---

## Inputs

* User request — authoritative for intent
* Existing proposals in `openspec/changes/` — for terminology and convention only
* NEVER read source code — proposal is intent, not implementation grounding

---

## Fast-Forward Mode (opsx-ff)

When invoked from `/opsx-ff` context (or the user requests fast-forward / "keep momentum" / "skip questions"):

* Run Phase -1 Auto-Update Check NORMALLY — this is NEVER skipped.
* Skip Phase 1 interactive Q&A. Extract answers for the five lenses (Problem & Why, Goals, Scope & Capabilities, Non-Goals, Impact) from the user's description and any prior context.
* Where context is silent on a lens, make a reasonable assumption based on apparent scope. Document assumptions inline in the proposal where they affect the artifact.
* Run the decomposition check, format compliance, and Artifact Compliance Review NORMALLY — these are NOT skipped.
* Only pause for the user if a lens is CRITICALLY ambiguous in a way that materially changes the proposal's scope.

Detection: `/opsx-ff` in conversation OR explicit FF keywords ("fast-forward", "skip questions", "keep momentum", "just create everything", "all artifacts", "no questions"). Normal continuation words ("continue", "go", "proceed", "next", "yes", "ok") do NOT trigger FF — requires explicit intent.

---

## Workflow

Four phases. NEVER skip, merge, or reorder.

```text
Phase -1: Auto-Update Check (once per session)
Phase 0: Schema & Template Resolution
Phase 1: Interactive Discovery (5-lens Q&A)
Phase 2: Write & Artifact Compliance Review
```

---

## Workflow Visibility (MANDATORY)

Display workflow phases via task tool at start; update as each phase completes.

---

## Core Principles

- **Understand Before Defining** — seek the problem before describing the change; never jump to proposed solutions.
- **Goals Before Solutions** — solution language ("add Redis", "use Postgres") → redirect to the underlying problem and goal.
- **Scope Boundaries Are Critical** — non-goals as deliberate as in-scope items; strong non-goals reduce future scope creep.
- **YAGNI — No Speculative Scope** — every scope item traces to a stated user need or stakeholder concern.
- **Respect Project Standards** — read project instruction files (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, equivalents) at Phase 1 start; use project terminology; conflicts → surface to user, never silently override.

---

## Phase -1: Auto-Update Check (MANDATORY — once per session)

**Run FIRST, before Phase 0. Skip if already run this session.**

1. Read `openspec/.plus/last-update-check`:
   - File exists AND timestamp is within 7 days of today → **skip immediately, proceed to Phase 0**
   - Missing or older than 7 days → continue:
     - Fetch `https://raw.githubusercontent.com/sudokar/openspec-plus/main/VERSION`
     - Compare with `openspec/.plus/VERSION` (local)
     - If remote > local → display: `🔔 OpenSpec Plus update available (v{remote} → you have v{local}). Run install prompt: https://github.com/sudokar/openspec-plus#install--update`
     - Write current timestamp to `openspec/.plus/last-update-check`
2. Network/file errors → silently continue. Notification only — do NOT auto-install.

Mark Phase -1 complete, proceed to Phase 0.

---

## Phase 0: Schema & Template Resolution

Run FIRST (after Phase -1), before any questions:

```bash
openspec instructions proposal --change <name> --json
```

Extract from response:
- `template` — structural authority; sections you MUST fill
- `instruction` — per-section guidance; what content each section needs
- `rules` — project constraints to honor

Parse template sections (H2/H3 headers + HTML comments). These are your **information requirements**. Every template section = information you MUST collect during Phase 1. If a section needs data the five lenses don't cover, add questions for it.

---

## Phase 1: Interactive Discovery

**Pre-existing answers:** If recent conversation already answers any of the five lenses (Problem & Why, Goals, Scope & Capabilities, Non-Goals, Impact) — via prior exploration, a detailed initial request, or any other source — state the resolved answer in your analysis and SKIP that lens's question. Ask ONE question at a time only for unresolved or partial lenses. NEVER re-ask answered questions.

Read in this order:

1. **Project-level instruction files first** — `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, or equivalents at project root, `.claude/`, `.opencode/`, or `docs/`. These capture terminology conventions and project shape that should inform proposal language.
2. Existing proposals and changes in `openspec/changes/` for terminology and structural convention.

NEVER read source code.

If the request describes multiple independent subsystems (e.g., "add chat, file storage, billing, analytics"): STOP. Surface to user. Help decompose: what are the independent pieces, how do they relate, what order to build? Each sub-change gets its own proposal → spec → design → tasks cycle. Begin the first sub-change through normal flow. NEVER write a single proposal covering multiple independent subsystems.

Use the **question tool**, ONE question at a time. Cover these lenses until each is answered:

1. **Problem & Why** — what problem exists, why it matters, what happens without action
2. **Goals (not solutions)** — desired outcome; if request is phrased as a solution, what is the underlying goal
3. **Scope & Capabilities** — high-level changes; capabilities added, modified, or removed
4. **Non-Goals** — what is explicitly NOT in scope; scope creep to prevent
5. **Impact** — affected systems, files, teams, or workflows

NEVER batch questions. NEVER assume answers. Mark a recommended option "(Recommended)" when defaults exist.

Once all five lenses answered, summarize for the user before writing: problem (one sentence), outcome (one sentence), in-scope capabilities, non-goals, impact areas.

**Phase 1 Complete Checks:**
- **Mid-discovery scope check:** re-evaluate — did scope grow beyond a single cohesive change during Q&A? If so, decompose before writing.
- **Template coverage check:** verify every template section (from Phase 0) has collected substance to fill it; ask targeted questions if any section lacks substance.
- **Rules compliance check:** review `rules` from Phase 0; if any rule constrains what can be proposed, surface the conflict to the user before writing.

---

## Phase 2: Write & Artifact Compliance Review

Use the `template` and `outputPath` from Phase 0. Use `template` structure EXACTLY. Never improvise sections, restructure, or add headers the template does not include. Apply `instruction` and `rules` as constraints — do NOT copy them into the file.

### 2.1 Write Proposal

**Before writing — 2 mandatory steps:**

**Step 1 — Map Phase 1 to template:** Phase 1 answers are in context — use them directly. Do NOT extract, summarize, or rephrase. For each template section (from Phase 0), map the full Phase 1 answer unchanged. Nothing left unmapped.

**Step 2 — Density check:** Proposal must be at least as dense as Phase 1. Significantly shorter = information loss.

Write from the mapping. Do NOT discard any Phase 1 answer.

**CRITICAL — Missing or underrepresented information propagates as blind spots into all downstream artifacts.** Every Phase 1 answer must appear with full weight and specificity intact.

### 2.2 Artifact Compliance Review (MANDATORY — single-shot)

Dispatch subagent of type `general` (use your subagent/task tool) with reviewer prompt below. Subagent loads written proposal into its own context, returns structured findings list, exits.

**Discipline:**

* Single-shot — dispatch once, get findings, fix inline in root, surface for user review
* NEVER re-dispatch after fixing
* NEVER skip subagent because "proposal looks fine to me"
* NEVER reload proposal into root for review — defeats the purpose

#### Reviewer Subagent Prompt

```
You are a proposal document reviewer for an OpenSpec change. Verify the proposal is
clean, intent-level, and ready for user review.

Inputs:
- Proposal file: <PROPOSAL_PATH>
- Template: <TEMPLATE_CONTENT_FROM_PHASE_0>

Read the proposal before reviewing. Check each category:

| Category | What to look for |
|---|---|
| Solution language | Why or Goals sections describe WHAT not HOW — no implementation detail |
| Tech selection | No databases, frameworks, or tools named without justification |
| Design leaks | No architecture, components, schemas, contracts, or workflows |
| Implementation leaks | No tasks, sequences, estimates, milestones, or execution plans |
| Placeholders | No TBD, TODO, "[fill in]", incomplete sections |
| Non-goals | At least one non-goal explicit |
| Scope cohesion | No independent subsystems crept in — single cohesive change |
| YAGNI | Every scope item traces to a stated user need — no "while we're at it" additions |
| Template compliance | Artifact sections match the provided template — no improvised, missing, or reordered sections |
| Terminology consistency | Same concept named consistently throughout |

Calibration: only flag issues that would mislead downstream phases or
cause spec/design to build the wrong thing. Minor wording improvements
and stylistic preferences are NOT issues.

Return format:

Status: Approved | Issues Found

Issues (if any):
- [Category]: [specific finding] — [why it matters]

Recommendations (advisory, do not block):
- [optional improvement suggestions]
```

After receiving reviewer's response:

* Status Approved → surface proposal for user review
* Status Issues Found → fix each Issue inline in root context, surface for user review (NEVER re-dispatch)

---

## Anti-Patterns

NEVER propose architectures, components, services, APIs, schemas, contracts, or workflows. NEVER recommend technologies, frameworks, databases, or infrastructure. NEVER create tasks, milestones, work breakdowns, execution plans, or sequences. NEVER read source code. NEVER write a proposal covering multiple independent subsystems — decompose first.

Implementation details appear — redirect to the corresponding downstream phase.

---

## Success Criteria

**Succeeds:** problem clear, goals separated from solutions, scope/non-goals explicit, impact identified, format compliant, reviewer dispatched once with findings applied.

**Fails:** written before lenses answered, multiple independent subsystems in one proposal, solution/tech/design/implementation language present, reviewer skipped/re-dispatched.
