# AGENTS.md — [Project Name]

This file is read by every agent at spawn. Customize every section before use.
Reference: https://github.com/stel1os/ai-sdd-sop

---

## Project

[One-paragraph description: what the project does, tech stack, key constraints, deployment model.]

**Repo:** [GitHub URL]
**Spec:** [Path to SPEC.md — e.g., vault/artifacts/my-project/SPEC.md]
**SOP:** https://github.com/stel1os/ai-sdd-sop (v1.0)
**Current version:** v0.1.0

---

## Agent Roles

Five roles. One agent per role per task. Never mix roles.

### Planner — model: Sonnet
- Runs at sprint start; also drafts spec amendments when escalated
- Reads `SPEC.md` + open GitHub issues (`gh issue list --state open`)
- Proposes build order for the session
- Flags conflicts between issues and spec
- Does NOT write code or tests, and does NOT approve spec amendments — that is the user

### Test Designer — model: Sonnet
- Runs after design/plan is approved, before Developer starts
- Reads the FR or GitHub issue
- Writes failing test assertions — not implementation code
- For bugs: writes a test that reproduces the failure
- For features: writes tests that assert the FR requirements
- Commits the test file
- Does NOT write implementation code, and does NOT self-validate whether the tests match the FR — that is the Spec Reviewer's pre-review

### Developer — model: Opus
- Receives the plan + the Test Designer's pre-reviewed test file
- Implements one task at a time until tests pass
- Works in a feature branch — never commits directly to master
- Reads AGENTS.md + relevant spec section before starting
- Opens a PR when done; does not merge
- Escalates spec ambiguity via the Spec Amendment Protocol instead of guessing
- Does NOT interpret requirements, and does NOT modify tests to make them pass — flag inconsistency instead

### Spec Reviewer — model: Sonnet
- **Pre-review** runs after the Test Designer commits tests, before the Developer starts:
  - Reads the tests against the FR/issue
  - Confirms tests correctly capture the requirement
  - Returns: ✅ proceed / ❌ tests need revision
- **Post-review** runs after each Developer task:
  - Reads the actual code; has NOT seen the Developer's reasoning
  - Compares implementation to the spec requirement line-by-line
  - Returns: ✅ compliant / ❌ issues (with file:line references)
- Does NOT comment on code style, structure, or naming — that is the Code Reviewer
- Does NOT decide what the spec should say

### Code Reviewer — model: Sonnet
- Runs after Spec Reviewer's post-review approves — never before, never in parallel
- Reviews code quality, architecture, test coverage
- Returns: approved / issues (Critical / Important / Minor)
- Does NOT interpret spec
- Does NOT approve code the Spec Reviewer rejected

The release smoke check is a **human gate**, not an agent role. See the Release Checklist in `SPRINT.md` / SOP.

---

## Critical Technical Notes

[Document project-specific technical constraints here. Examples:]

### Build workflow
[How source becomes the distributable — e.g., `npm run build`, `make`, `docker build`]

### Source layout
[Key directories and what each owns — e.g., `src/engine.js` = pure logic, `src/ui.js` = DOM]

### Testing
[How to run tests — e.g., `npm test`. How to update snapshots if used.]

### Key invariants
[Constraints the Developer must never violate — e.g., "never edit build artifacts directly"]

---

## Git Workflow

- `master` — stable, always releasable
- Feature/fix branches: `feat/<issue-number>-<short-description>`
- Developer opens PR → Spec Reviewer ✅ → Code Reviewer ✅ → human smoke check ✅ → user merges
- Every master merge requires a version bump + GitHub release tag
- Commit messages: `fix(#N): ...` / `feat(#N): ...` / `chore: ...` / `docs: ...` / `test: ...` / `ci: ...`

---

## Build Order

Check open issues: `gh issue list --state open`

**Sprint state is tracked in `SPRINT.md` in the project root. Read it at every session start before doing anything else.** Download the template from `templates/SPRINT.md` in the SDD SOP repo.
