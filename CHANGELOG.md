# Changelog

All notable changes to the SDD SOP are documented here.

---

## v1.4.0 — 2026-06-11

### Changed: working documents are now committed (reverses a v1.0 decision)

`SPEC.md`, design docs, plans, and `AGENTS.md` are committed; only `SPRINT.md` stays gitignored. The v1.0 rationale ("working documents are AI working memory, not product deliverables") was not wrong, but it left the spec — the SOP's own source of truth — as the only load-bearing document without change control. The Spec Amendment Protocol's "user approves the diff" is now a literal git diff: step 5 becomes a `docs(spec):` commit. `AGENTS.md` carries a note to keep machine-specific paths out of it. Rule 8, the Document Stack, and all setup steps updated.

### Changed: durable model tiers replace model names

Roles now specify **fast / standard / frontier** tiers instead of Haiku/Sonnet/Opus, ending the stale-model-name maintenance burden (v1.3.1 was already such a fix). One mapping footnote (as of 2026-06: fast = Haiku, standard = Sonnet, frontier = Opus/Fable) is the only line that ever goes stale. Test Designer remains frontier per the v1.3.0 reasoning-load rationale.

### Changed: task-complexity tiering is now the default

The mechanical/standard/architectural tier table moved from Scaling bottleneck #1 into the main Five Roles section. The plan doc classifies every task; unclassified tasks default to standard. Developer and reviewer tiers are "per task". Skipping the Test Designer for `chore:`/`docs:`/`ci:` commits is likewise now the default rather than a scaling mitigation.

### Added: Review Rejection Loop

The previously undocumented most-common path. A reviewer rejection produces a findings document (file:line + criterion); a fresh Developer (or Test Designer) context receives plan + tests + findings; the same reviewer role re-reviews; two failed cycles on one task escalate to the user, since repeated rejection usually signals a spec or plan defect.

### Added: Human Gates table + `⏳ awaiting user` state

Explicit table of which gates require human sign-off (spec content/amendments, design doc, plan doc, version bump type, smoke check, PR merge, release tag) vs. AI-autonomous gates (reviewer verdicts). New `⏳ awaiting user` gate state in the Session Start Protocol and `templates/SPRINT.md`: agents stop at a human gate and do not continue until the user responds.

### Changed: structural conciseness pass

README shrank from 363 to ~290 lines despite the additions. "For AI Agents Adopting This SOP", "New Project Setup", and "Using This SOP" merged into one "Adopting This SOP" section; feature and bug-fix sprint diagrams merged with inline variants; the Spec Compliance Review section folded into the Spec Reviewer role row; Scaling compressed to one-line mitigations; spec-kit section compressed. Eight Rules, Working Rules, role boundaries, protocols, and checklists intact. Claude Code skill names (`/brainstorm`, `/writing-plans`) replaced with generic phase names; npm commands generalized.

---

## v1.3.1 — 2026-06-04

### Fixed: stale version references across repo files

- `templates/AGENTS.md` — SOP reference updated from `v1.0` to `v1.3`
- `README.md` Five Roles table — Test Designer and Developer model columns corrected to match `templates/AGENTS.md` (Test Designer: Sonnet → Opus; Developer: Opus → Sonnet); these were missed in v1.3.0

---

## v1.3.0 — 2026-06-04

### Changed: templates/AGENTS.md — model assignments

Swapped model assignments for Test Designer and Developer based on reasoning load, not implementation complexity.

- **Test Designer:** Sonnet → Opus. The Test Designer has the least constrained reasoning task in the pipeline — it must interpret an FR or issue and decide what correct behavior looks like with no existing artifact to check against. Mis-specified tests are worse than no tests; Opus reduces that risk.
- **Developer:** Opus → Sonnet. The Developer's job is the most constrained: it receives a detailed plan and a pre-reviewed test file, and its success criterion is unambiguous (make the tests pass without modifying them). Sonnet is sufficient for execution against a known target. Opus may still be used per-task for genuinely complex implementation, but is not the default.

---

## v1.2.0 — 2026-05-25

### Added: templates/CLAUDE.md

New optional behavioral starter for projects whose toolchain auto-loads `CLAUDE.md` (Claude Code, Cursor, etc.). Contains four working rules adapted from Andrej Karpathy's January 2026 CLAUDE.md framework — Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution — in the SOP's existing terse voice.

`AGENTS.md` remains the canonical tool-agnostic config. `CLAUDE.md` is a supplement, not a replacement.

### Added: README — "For AI Agents Adopting This SOP"

New top-level section placed after "Why SDD". Concrete numbered steps for AI agents reading the SOP to set up a new project. Closes a discoverability gap — the existing "Using This SOP" section is human-targeted and lives at the bottom.

### Changed: README Working Rules aligned with templates/CLAUDE.md

The four rules in `## Working Rules` now match `templates/CLAUDE.md` verbatim — Think before coding / Simplest solution first / Surgical changes only / Plan, then verify. Rules 1–3 are rewordings of the v1.1.0 rules (same intent, sharper wording from the Karpathy framework). Rule 4 (Plan, then verify) is new and adds explicit pre-task planning with verification checkpoints; it replaces the prior "Flag uncertainty explicitly" rule, whose intent is now absorbed into rule 1.

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
