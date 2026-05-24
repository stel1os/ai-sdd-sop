# Changelog

All notable changes to the SDD SOP are documented here.

---

## v1.1.0 — 2026-05-24

### Added: Working Rules

New `## Working Rules` section added after `## The Eight Rules`. Four rules governing agent behavior at all times, regardless of role or sprint phase: ask don't assume, simplest solution first, don't touch unrelated code, flag uncertainty explicitly. Sourced from established project working rules to make them SOP-canonical.

---

## v1.0.0 — 2026-05-21

Initial release.

### Document stack
`AGENTS.md` (project config) · `SPEC.md` (numbered FRs) · design doc · plan doc · `SPRINT.md` (live session tracker) · tests under `test/` · code under `src/`. Working documents are gitignored in the consuming project.

### Five roles
Planner · Test Designer · Developer · Spec Reviewer · Code Reviewer. Each with explicit `Does` and `Does NOT` boundaries. Release smoke check is a human gate, not an agent role.

### Lifecycle
Feature sprint · Bug fix sprint · Spec Amendment Protocol (user-gated path for mid-sprint spec changes) · Release Checklist.

### Reviews
Spec Reviewer pre-reviews the test file against the FR before the Developer starts. After each task, Spec Reviewer (compliance) then Code Reviewer (quality); both must pass.

### Session Start Protocol
Every session begins by reading `AGENTS.md` then `SPRINT.md`, reporting position in one sentence, and continuing from the first incomplete gate.

### The Eight Rules
Spec first · No code outside sprint · Tests precede code · Pre-review the tests · Two-gate review per task · Tests trace to spec · Version bump on every master merge · Source is authoritative.

### Templates
`templates/AGENTS.md` · `templates/SPRINT.md`. Tool-agnostic project starters.

### Scaling
Six bottlenecks with mitigations (tiered models, parallel post-review, automated smoke check, skip Test Designer on chores, spec directory split, parallel Developers). Threshold table by project size.

### Relationship to github/spec-kit
Mapping table to spec-kit's Specify → Plan → Tasks → Implement flow, and explicit list of where SDD extends it.
