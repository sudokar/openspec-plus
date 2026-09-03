# Changelog

All notable changes to OpenSpec Plus will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.6.1] - 2026-09-03

### Fixed
- `openspec-plus-apply`: dispatch templates leaked the envelope wrapper (description/prompt framing) into the subagent's actual prompt text, which could cause a worker to attempt spawning its own subagent and fall back to a visible task-creation tool instead. Fixed by keeping only the literal prompt content inside the fence and adding an explicit "do this yourself, don't delegate" instruction to each template.

---

## [1.6.0] - 2026-09-02

### Changed
- Skills frontmatter restructured: `version`, `priority`, and `disable-user-invocation` moved under `metadata` section per [agentskills.io specification](https://agentskills.io/specification#skill-md-format)
- All skill versions unified to `1.6.0` (release version)
- Proposal skill auto-update check now uses GitHub Releases API (`/releases/latest`) instead of fetching the VERSION file — removes dependency on VERSION file location

---

## [1.5.0] - 2026-08-30

### Added
- New `openspec/.plus/config.yaml` — optional, project-level settings file, separate from `openspec/config.yaml`, installed/merged automatically by the install/update prompt.
- `settings.questionMode: batch` — `openspec-plus-proposal`/`-design`/`-spec`/`-tasks` group each phase's discovery questions into as few question-tool rounds as possible instead of one at a time. Same real answers, no assumptions — fewer round trips for per-prompt-priced model providers, especially premium/high-tier models where round trips are expensive. Config-only, no conversational trigger.
- `settings.autoUpdateCheck: false` — lets `openspec-plus-proposal` skip its weekly network version check + upgrade question entirely.
- `settings.apply.executionMode` / `settings.apply.parallelism` — let `openspec-plus-apply` skip its Subagent-vs-Inline and parallel-dispatch questions when set.

---

## [1.4.1] - 2026-08-30

### Fixed
- `openspec-plus-proposal`/`-design`/`-spec`/`-tasks`: removed unreliable Fast-Forward Mode detection from activation triggers — fuzzy keyword matching could accidentally skip mandatory Q&A in /opsx-new and /opsx-continue; /opsx-ff now falls back to vanilla OpenSpec behavior
- `openspec-plus-apply`: reviewer subagent prompts (code-quality, final-review, spec-compliance) now explicitly mark the walkthrough section as working analysis only and instruct the subagent to output ONLY the Return Format as its final answer, preventing verbose intermediate analysis from leaking into the apply phase output

---

## [1.4.0] - 2026-07-23

### Changed
- `openspec-plus-apply`: enforces verbatim subagent prompt dispatch (Dispatch Contract), inlines spec content via `{SPECS_TEXT}`, adds explicit VERIFY-RED step to TDD loop, drops `TDD-Discipline` review category, and tightens per-item enumeration discipline in spec-compliance review
- `openspec-plus-design`: adds mandatory Session Context Fidelity Check (Phase 4.2) before compliance review to catch missing decisions and rationale
- `openspec-plus-spec`: adds mandatory Session Context Fidelity Check (Phase 3.3) before compliance review; questions now require a recommendation; explicit user confirmation before advancing phases
- `openspec-plus-proposal`: questions now require a recommendation with rationale; explicit user confirmation before advancing phases; agents resolve discoverable facts from artifacts instead of asking
- `openspec-plus-tasks`: slice/task discipline criteria reformulated around Slice, Task, WHAT-not-HOW, and Behavior-only principles
- `openspec-plus-tdd`: minor wording cleanup; no behavioral change

---

## [1.3.0] - 2026-07-06

### Changed
- `openspec-plus-proposal`: Phase -1 upgrade notification replaced with an interactive question-tool prompt — users now choose **Upgrade (Recommended)** or **Upgrade later** instead of receiving a passive notification that was easy to miss; selecting Upgrade surfaces the install/update link and stops the session so the user upgrades before continuing.

---

## [1.2.1] - 2026-07-03

### Changed
- Subagent dispatch: accurate tool+type mapping per agent, self-detect fallback for unlisted/future agents, corrected Claude Code (`Agent`), OpenCode (`@general`), Windsurf/Devin split; skill-tool fallback added; reduced note token usage ~3×

---

## [1.2.0] - 2026-07-02

### Added
- `openspec-plus-apply`: explicit TDD-discipline checks and cross-task/cross-slice refactor checks.

### Changed
- `openspec-plus-apply`: implementer and reviewers now read spec/design artifacts directly from passed paths.
- `openspec-plus-apply`: review flow reruns per-slice gates after fixes before re-review.
- `openspec-plus-apply`: inline and final review are more focused on relevant conventions, artifact fidelity, and cross-slice integration.

---

## [1.1.1] - 2026-06-30

### Fixed
- `openspec-plus-tdd`: reverted skill body to 1.0.0 content — the 1.1.0 simplifications caused the RED→GREEN→REFACTOR cycle to not be followed strictly by some models.

### Changed
- `openspec-plus-tdd`: frontmatter `version` bumped to `1.0.1` (signals deliberate revert, preserves history); `disable-user-invocation: true` retained from 1.1.0 — correct and orthogonal to the content regression

---

## [1.1.0] - 2026-06-30

### Added
- `disable-user-invocation: true` frontmatter field to all six skill files — prevents direct user invocation; skills activate only via their trigger conditions
- Library Resolution gate between Phase 2 and Phase 3 in `openspec-plus-design` — libraries are surveyed and approved after approach selection, before any section is built, so all Phase 3 sections have resolved library context

### Changed
- `openspec-plus-design`, `openspec-plus-proposal`, `openspec-plus-spec`, `openspec-plus-tasks`: replaced 3-step scan/extract/map write process with direct 2-step map-from-context approach — eliminates retrospective summarisation that caused information and structure loss in written artifacts
- `openspec-plus-design`: added "Structure is content — preserve the form" principle before write steps; Step 2 structural fidelity sub-check ensures tables, diagrams, and structured layouts are never collapsed to prose
- `openspec-plus-spec`: added "Structure is content" preamble and structural fidelity sub-check specifically covering Gherkin scenario preservation
- `openspec-plus-design`: Phase 3 section approval instruction now explicitly states Phase 1-3 outputs are in context and must be used directly without re-extraction
- `openspec-plus-proposal`: Phase -1 Auto-Update Check logic rewritten with explicit early-exit branch — file exists and timestamp within 7 days → skip immediately, preventing unnecessary version fetches

### Fixed
- `openspec-plus-proposal` Phase -1 skipping logic: version check was running even when `last-update-check` timestamp was within 7 days due to missing explicit skip path
- `openspec-plus-design` artifact information loss: design artifact was missing tables, ASCII diagrams, and structured content from Phase 3 discussions because the write step triggered retrospective context compression

## [1.0.1] - 2026-06-24

### Changed
- Replaced `BASE_SHA`/`HEAD_SHA` comparison with `git diff HEAD` + changed file paths in code-quality reviewer, final reviewer, and apply orchestrator — adds fallback to direct file reading when git is unavailable

### Fixed
- Spec reviewer no longer flags GIVEN/WHEN/THEN/AND/BUT scenario format as a template compliance issue when the OpenSpec template uses simpler WHEN/THEN format

---

## [1.0.0] - 2026-06-22

### Added
- Initial release of OpenSpec Plus
- `openspec-plus-proposal` - Enhanced proposal phase with discovery lenses and alignment gates
- `openspec-plus-spec` - Interactive specification workflow with Gherkin scenarios
- `openspec-plus-design` - Structured design decision documentation
- `openspec-plus-tasks` - Detailed task breakdown with acceptance criteria
- `openspec-plus-apply` - Efficient execution of tasks with strict TDD implementation workflow
- `openspec-plus-tdd` - Enforces RED-GREEN-REFACTOR cycle per test
- VERSION file for version tracking
- CHANGELOG.md for release notes

### Changed
- N/A (initial release)

### Deprecated
- N/A

### Removed
- N/A

### Fixed
- N/A

### Security
- N/A

---

## How to Update

Run the [install prompt](README.md#install--update) again to get the latest version.

The skills will automatically notify you when updates are available.
