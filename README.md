# Spec-Driven Development (SDD)
### A Standard Operating Procedure for AI-Assisted Software Projects

**Version:** v1.3.0 — 2026-06-04

> Every design decision, implementation task, and test traces back to a spec requirement.
> The spec is updated when requirements change — not the code.

---

## Why SDD

AI coding agents are fast but untethered. Without a spec as ground truth, they build plausible-looking things that don't match what was intended. Mandatory spec compliance review at every task makes drift visible and correctable before it compounds.

SDD borrows the **mindset** of change control: write what you intend to build, build exactly that, verify it, release it. No improvisation. No scope creep. No "I thought you meant…"

This is internal workflow discipline, not GxP. There is no external auditor, no signed deviation, no CAPA. The rigor comes from the process being followed, not from a regulator enforcing it.

---

## For AI Agents Adopting This SOP

If you are an AI agent reading this SOP to set up or join a project, execute these steps in order. Do not skip ahead.

1. **Read first, act later.** Read the **Document Stack**, **Five Roles**, **Session Start Protocol**, **Lifecycle**, **Eight Rules**, and **Working Rules** sections below before doing anything else.
2. **Wait for an explicit user signal** before scaffolding the project, writing `SPEC.md`, or invoking any role. The SOP is the procedure; the user is the authority.
3. **Download templates** from this repo into the project root (only when the user authorizes setup):
   - `templates/AGENTS.md` → `AGENTS.md` — tool-agnostic project config (required)
   - `templates/SPRINT.md` → `SPRINT.md` — sprint state tracker (required; gitignore it)
   - `templates/CLAUDE.md` → `CLAUDE.md` — behavioral rules (optional; recommended if your toolchain auto-loads `CLAUDE.md`, e.g. Claude Code, Cursor)
4. **Fill in every placeholder** in `AGENTS.md`. Never commit with `[bracketed]` text remaining. Ask the user when a placeholder has no obvious answer.
5. **Verify with the user** before writing `SPEC.md`. The spec is user-authored or user-approved line by line; AI does not write specs unilaterally.
6. **At every session start** after setup, follow the **Session Start Protocol**: read `AGENTS.md`, then `SPRINT.md`, then report position in one sentence before continuing.

The three project files form a layered config:

| File | Defines | Who edits | Updated |
|------|---------|-----------|---------|
| `CLAUDE.md` (optional) | **How** you work — behavioral rules | User | Rarely |
| `AGENTS.md` | **What** this project is — roles, build, invariants | AI + User | Stable across a sprint |
| `SPRINT.md` | **Where** the project is right now — gates | AI | At every gate |

Never invent state, never skip the protocol, never silently reinterpret a requirement.

---

## Document Stack

Every project has exactly these layers, in this order:

| Layer | File | Owner | Purpose |
|-------|------|-------|---------|
| **Config** | `AGENTS.md` (repo root, gitignored) | AI + User | Machine-readable project config — roles, technical notes, git workflow. Read by every agent at spawn. Stable across a sprint. |
| **Spec** | `SPEC.md` | User | Source of truth — WHAT to build. Numbered requirements (FR-001, FR-002, …). Never derived from code. |
| **Design** | `specs/YYYY-MM-DD-<topic>-design.md` | AI + User | HOW to build a specific feature. Per sprint, user-approved before implementation. |
| **Plan** | `plans/YYYY-MM-DD-<topic>.md` | AI | Step-by-step task breakdown. References the design doc. |
| **State** | `SPRINT.md` (repo root, gitignored) | AI | Live sprint tracker. Updated at every gate. Single source of truth for "where are we." |
| **Tests** | `test/` (in repo) | AI (Test Designer) | Verify spec requirements. Written from FRs before implementation. Traceable to an FR or invariant. |
| **Code** | `src/` (in repo) | AI (Developer) | Implementation. Must match plan → design → spec. |

**Working documents (SPEC, design, plan, SPRINT, AGENTS) live alongside the code but are gitignored.** They are AI working memory and project config, not product deliverables.

---

## The Five Roles

One agent per role per task. Roles never mix.

| # | Role | Phase | Model | Does | Does NOT |
|---|------|-------|-------|------|----------|
| 1 | **Planner** | Sprint start; spec amendments | Sonnet | Reads spec + open issues. Proposes build order. Flags spec conflicts. Drafts spec amendments when escalated. | Write code or tests. Approve amendments — that is the user. |
| 2 | **Test Designer** | Before implementation | Sonnet | Reads the FR or issue. Writes failing test assertions. Commits the test file before Developer starts. | Write implementation code. Decide whether the test correctly captures the FR — that is the Spec Reviewer's pre-review. |
| 3 | **Developer** | Implementation | Opus | Receives plan + pre-reviewed tests. Implements until tests pass. Opens PR. Escalates spec ambiguity instead of guessing. | Merge. Interpret requirements. Modify tests to make them pass — escalate inconsistency instead. |
| 4 | **Spec Reviewer** | (a) before Developer starts, (b) after each task | Sonnet | **Pre-review:** reads the Test Designer's tests against the FR; approves or returns to Test Designer. **Post-review:** reads the actual code; compares to spec line-by-line; every finding cites file:line. | Comment on code style, structure, or naming — that is the Code Reviewer. Decide what the spec should say. |
| 5 | **Code Reviewer** | After Spec Reviewer approves | Sonnet | Reviews code quality, architecture, test coverage. Returns Critical / Important / Minor. | Second-guess spec requirements. Approve code the Spec Reviewer rejected. Run in parallel with Spec Reviewer. |

The **release smoke check** is a human gate, not an agent role — see the Release Checklist.

**Independence rules:**
- Developer never sees Spec Reviewer reasoning — fresh context per role.
- Spec Reviewer always runs before Code Reviewer, never in parallel.
- **Limit:** independence reduces context bleed, not pattern-matching bias. Two Sonnets share training and will make correlated errors. Mitigations: use different model tiers across roles when budget allows; treat the human gates (spec approval, PR review, release smoke check) as the real backstop, not agent independence.

---

## Session Start Protocol

**Every session begins here — before any other action.**

1. Read `AGENTS.md` for the project.
2. Read `SPRINT.md` in the project root.
3. Report current position in one sentence: *"We are at [gate], [what is done], next step is [X]."*
4. Continue from the first incomplete gate. Do not restart completed steps.

If `SPRINT.md` shows **Idle**, ask the user what to work on. If it shows a sprint **in progress** with no open blockers, continue without asking — the checklist is the instruction.

`SPRINT.md` is the single source of truth for project position. Mark each gate complete in `SPRINT.md` before moving to the next. Never batch updates. `AGENTS.md` is stable config; it is not edited mid-sprint.

---

## Lifecycle

### New Project Setup

1. Write `SPEC.md` — product vision, versioning strategy, numbered FRs. User writes or approves every line.
2. Download `templates/AGENTS.md` from this repo into the project root. Fill in every placeholder.
3. Download `templates/SPRINT.md` into the project root. Add it to `.gitignore`.
4. **(Optional)** Download `templates/CLAUDE.md` into the project root if your toolchain auto-loads `CLAUDE.md` (Claude Code, Cursor). It provides four behavioral working rules.
5. Point your AI tool at `AGENTS.md` using your toolchain's convention. Every agent reads it at spawn.
6. Set up the repo: `src/`, `test/`, `samples/` (or equivalent), `package.json` with `build` and `test` scripts.
7. Create an initial regression test suite (even if empty) before writing any feature code.

### Feature Sprint

```
User: "I want X"
  ↓
Planner  →  reads spec + issues, proposes build order
  ↓
/brainstorm  →  design doc  →  user approves
  ↓
/writing-plans  →  plan doc  →  user approves
  ↓
Test Designer  →  writes failing tests from FRs  →  commits test file
  ↓
Spec Reviewer (pre)  →  do the tests match the FR? ✅ or back to Test Designer
  ↓
Developer  →  implements until tests pass, task by task
  ↓
Per task: Spec Reviewer (post) ✅  →  Code Reviewer ✅
  ↓
Version bump  →  build  →  PR  →  human smoke check ✅  →  merge  →  release tag
```

### Bug Fix Sprint

```
Bug discovered (failing test or user report)
  ↓
Open issue: symptom, root cause, repro case, fix suggestion
  ↓
Test Designer  →  writes failing test that reproduces the bug
  ↓
Spec Reviewer (pre)  →  does the test correctly capture the bug?
  ↓
/writing-plans  →  plan doc
  ↓
Developer  →  fixes until that test passes
  ↓
Per task: Spec Reviewer (post) ✅  →  Code Reviewer ✅
  ↓
npm run test:update  →  review snapshot diff  →  commit
  ↓
Version bump  →  build  →  PR  →  smoke check ✅  →  merge  →  release tag  →  close issue
```

### Spec Amendment Protocol

Requirements crystallize through implementation. When the Developer or a reviewer finds the spec is ambiguous, incomplete, or contradicts an existing FR:

1. **Developer pauses.** Implementation halts at the current task. No guessing.
2. **Open a spec issue.** Title: `spec: clarify FR-xxx — <one-line summary>`. Body: the ambiguity, candidate interpretations, why it matters now.
3. **Planner drafts the amendment.** Proposes a revised FR or a new one. References the original.
4. **User approves the diff.** Spec amendments are user-gated. AI does not approve spec changes.
5. **`SPEC.md` updated.** The affected FR carries a `Revised YYYY-MM-DD — see issue #N` line.
6. **Tests revisited.** Test Designer re-evaluates the test file; Spec Reviewer pre-reviews again.
7. **Developer resumes.**

Amendments are normal, not exceptional. Silent reinterpretation is the failure mode this protocol prevents.

### Release Checklist

- [ ] All tests pass (`npm test` or equivalent)
- [ ] Build succeeds from source
- [ ] Version bumped (semver: patch for bugs, minor for features, major for breaking)
- [ ] PR opened, CI passes
- [ ] **Human smoke check** — open the built artifact, walk the sprint's user-facing flows from a checklist, confirm pass/fail per item. This is a human gate until automated E2E coverage exists. When E2E exists, it becomes a CI job.
- [ ] Merged to master/main
- [ ] Release tagged (`gh release create vX.Y.Z`)
- [ ] User-facing changelog updated
- [ ] Issues closed
- [ ] `SPRINT.md` reset to Idle

---

## Testing Contract

Tests must be **traceable**. Every assertion points to one of:

- A **spec requirement** (FR-xxx): `assert firstLump === n_months * budget - sum_paid // FR-043`
- A **structural invariant**: balance is always non-negative, loan always terminates
- A **golden snapshot**: captures correct output; fails when output drifts unexpectedly

**Tests are never updated silently.** Snapshot updates require a deliberate command, a diff review, and a commit. The snapshot diff is the verification record.

**After each bug fix:** regenerate snapshots, read the diff, confirm the delta matches the expected fix, commit.

---

## Spec Compliance Review

The Spec Reviewer reads the actual code and checks:

| Check | Question |
|-------|----------|
| Nothing missing | Did the Developer implement every requirement in the task? |
| Nothing extra | Did the Developer add anything not requested? |
| No misinterpretation | Did the Developer solve the right problem? |
| Correct behaviour | Does the code do what the spec says, not just what looks plausible? |

A "looks good" verdict without reading the code is invalid. Every finding cites file:line.

---

## Git Workflow

- `master` — always stable and releasable
- Feature/fix branches: `feat/<issue-number>-<short-description>`
- Every master merge requires a version bump + release tag (no exceptions)
- Commit prefixes: `fix(#N):` / `feat(#N):` / `chore:` / `docs:` / `test:` / `ci:`
- Never edit build artifacts directly — always edit source then rebuild

---

## The Eight Rules

1. **Spec first.** No implementation without an approved design doc (features) or issue (bugs).
2. **No code outside sprint mode.** Discoveries go to issues. Nothing is "just a quick fix."
3. **Tests precede code.** The Test Designer commits the test file before the Developer starts.
4. **Pre-review the tests.** The Spec Reviewer checks the test file against the FR before implementation begins. Wrong tests lock the Developer to a wrong target.
5. **Two-gate review per task.** Spec compliance, then code quality. Both must pass before the next task.
6. **Tests trace to spec.** If you can't name the FR or invariant a test covers, the test is wrong.
7. **Version bump on every master merge.** Including hotfixes and docs-only changes that affect the build artifact.
8. **Source is authoritative.** Build artifacts are generated from `src/`, never edited. Working documents (SPEC, design, plan, SPRINT, AGENTS) are gitignored — they are intent and state, not deliverables.

---

## Working Rules

These rules govern agent behavior during any task. They apply regardless of role or sprint phase. They are reproduced verbatim in `templates/CLAUDE.md` so that toolchains which auto-load `CLAUDE.md` (Claude Code, Cursor) get them at session start.

1. **Think before coding.** State assumptions explicitly. When a request is ambiguous, present multiple interpretations and ask — never guess.
2. **Simplest solution first.** Implement the simplest thing that could work. Do not add abstractions, flexibility, or error handling that weren't explicitly requested.
3. **Surgical changes only.** Modify only what the task requires. Adjacent improvements need explicit authorization, even when the code looks wrong.
4. **Plan, then verify.** For any multi-step task, state a brief plan with verification checkpoints up front. Define success criteria so progress is measurable.

These rules are adapted from Andrej Karpathy's January 2026 CLAUDE.md framework (Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution).

---

## Scaling

SDD as described above is optimised for **a single developer + AI agents on a focused project**. Some parts scale naturally; others need adaptation as scope grows.

### What scales without changes

- The document stack (Config → Spec → Design → Plan → Tests → Code) works at any size
- The git workflow and release process are standard and proven
- Role separation maps cleanly to human teams — each role becomes a person or a sub-team
- The testing contract (invariants + snapshots + FR traceability) grows with the codebase

### Known bottlenecks and mitigations

**1. Agent cost per task.** Five agent invocations per task (Test Designer, Spec Reviewer pre, Developer, Spec Reviewer post, Code Reviewer) is expensive at scale. A 20-task sprint runs 100+ invocations before re-reviews.

*Mitigation:* Use tiered models. Mechanical tasks use a fast/cheap model for Developer and reviewers. Complex or architectural tasks use the most capable model. The plan document should classify tasks by complexity.

| Task type | Developer | Reviewers |
|-----------|-----------|-----------|
| Mechanical (1–2 files, clear spec) | Haiku | Haiku |
| Standard (multi-file, some judgment) | Sonnet | Sonnet |
| Architectural (design decisions, broad impact) | Opus | Sonnet |

**2. Sequential post-review gates.** Spec Reviewer → Code Reviewer is forced sequential. Two reviewers × sequential = 2× wall time.

*Mitigation:* For mechanical tasks with low spec ambiguity, run post-Spec and Code Review in parallel. Keep strict sequencing when the Spec Reviewer's verdict could change what the Code Reviewer evaluates.

**3. Human smoke check.** A checklist-driven browser walkthrough grows linearly with features. At 50+ flows it becomes a bottleneck and reliability risk (fatigue, inconsistent execution).

*Mitigation:* Replace the smoke check with automated end-to-end tests (Playwright, Puppeteer) once the project has a stable URL or a headless-runnable build. The smoke check becomes a CI job. Keep a short manual checklist only for flows genuinely hard to automate.

**4. Test Designer overhead on trivial tasks.** Running a Test Designer before every task — including version bumps, doc updates, and config changes — adds unnecessary round-trips.

*Mitigation:* Skip the Test Designer for `chore:`, `docs:`, and `ci:` commits. Apply it only to `feat:` and `fix:` commits where behaviour changes.

**5. Flat SPEC.md.** A single spec file works well up to ~80 FRs. Beyond that, navigation degrades and unrelated sections get reviewed unnecessarily.

*Mitigation:* Split into a spec directory when FRs exceed ~80:
```
spec/
  core.md
  ui.md
  engine.md
  integrations.md
```
Keep a `spec/index.md` listing all FR numbers with one-line summaries and links.

**6. No parallel implementation.** The current lifecycle is strictly sequential: one Developer at a time.

*Mitigation:* When a sprint contains two or more tasks with no shared state or file overlap, dispatch parallel Developers. Each works in its own git worktree. Reviewers run per-task as normal. Merge conflicts are the risk.

### Scaling thresholds (rough guidelines)

| Project size | Adjustment |
|---|---|
| < 50 FRs, < 5 active issues | SOP as written — no changes needed |
| 50–150 FRs, regular sprints | Tiered models + skip Test Designer for chores |
| 150+ FRs, multiple areas | Split SPEC.md + parallel Developers for independent tasks |
| Public-facing app with URL | Automate smoke check with E2E tests |
| Team > 1 human | Roles map to people; AI agents assist each role rather than filling it |

---

## Relationship to github/spec-kit

[`github/spec-kit`](https://github.com/github/spec-kit) is GitHub's official specification-driven development toolkit, published in May 2026. Its core flow — **Specify → Plan → Tasks → Implement** — maps directly to SDD's document stack.

| spec-kit | SDD equivalent |
|----------|---------------|
| Specify | `SPEC.md` (numbered FRs) |
| Plan | Design doc + Plan doc |
| Tasks | Plan doc task breakdown |
| Implement | Developer role + PR |

**Where SDD extends spec-kit:**

- **Formal role separation** — five named roles with explicit model recommendations; no role mixes phases
- **Test-before-implementation gate** — Test Designer writes failing tests from FRs before Developer starts (spec-kit has no test phase)
- **Pre-review of tests** — Spec Reviewer validates tests against the FR before implementation begins
- **Two-gate post-review per task** — Spec Reviewer (compliance) then Code Reviewer (quality); both must pass
- **Spec amendment protocol** — explicit user-gated path for mid-sprint spec changes
- **Release smoke check** — human gate on the built artifact before merge
- **Scaling guidance** — tiered models, parallel Developers, spec directory split

SDD is compatible with spec-kit tooling. If your project uses the spec-kit CLI or GitHub integration, use spec-kit for the Specify phase and follow SDD roles for the rest of the lifecycle.

---

## Using This SOP

1. **Download `templates/AGENTS.md`** from this repo into your project root. Fill in every placeholder.
2. **Download `templates/SPRINT.md`** into your project root. Gitignore it.
3. **(Optional) Download `templates/CLAUDE.md`** if your toolchain auto-loads it (Claude Code, Cursor). Four behavioral working rules; adapt to your project.
4. **Point your AI tool at `AGENTS.md`** using whatever convention your toolchain requires. Every agent reads it at spawn.
5. **Write `SPEC.md`** — product vision + numbered FRs. User writes or approves every line.
6. **Reference this repo** in your `AGENTS.md` and project `README.md` so the SOP version is traceable.
7. **Follow the lifecycle** above for every sprint.

`AGENTS.md` is the machine-readable layer the SOP mandates. How your AI tool loads it is determined by your toolchain, not this SOP. `CLAUDE.md` is an optional behavioral supplement for tools that auto-load it.

---

## Projects Using This SOP

| Project | Description |
|---------|-------------|
| [loan-tracker](https://github.com/stel1os/loan-tracker) | Zero-install browser-based mortgage tracker |

---

## License

MIT
