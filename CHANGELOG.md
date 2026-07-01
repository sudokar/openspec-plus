# Changelog

All notable changes to OpenSpec Plus will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
