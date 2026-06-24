# Changelog

All notable changes to OpenSpec Plus will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
