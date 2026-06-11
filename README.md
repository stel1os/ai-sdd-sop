# Spec-Driven Development (SDD)
### A Standard Operating Procedure for AI-Assisted Software Projects

**Version:** v1.4.0 — 2026-06-11

> Every design decision, implementation task, and test traces back to a spec requirement.
> The spec is updated when requirements change — not the code.

---

## Why SDD

AI coding agents are fast but untethered. Without a spec as ground truth, they build plausible-looking things that don't match what was intended. Mandatory spec compliance review at every task makes drift visible and correctable before it compounds.

SDD borrows the **mindset** of change control: write what you intend to build, build exactly that, verify it, release it. No improvisation. No scope creep. No "I thought you meant…"

This is internal workflow discipline, not GxP. There is no external auditor, no signed deviation, no CAPA. The rigor comes from the process being followed, not from a regulator enforcing it.

---

## Adopting This SOP

For AI agents and humans setting up or joining a project. Execute in order; do not skip ahead.

1. **Read first, act later.** Read the **Document Stack**, **Five Roles**, **Session Start Protocol**, **Lifecycle**, **Eight Rules**, and **Working Rules** below before doing anything else.
2. **Wait for an explicit user signal** before scaffolding, writing `SPEC.md`, or invoking any role. The SOP is the procedure; the user is the authority.
3. **Download templates** from this repo into the project root:
   - `templates/AGENTS.md` → `AGENTS.md` — tool-agnostic project config (required, committed)
   - `templates/SPRINT.md` → `SPRINT.md` — sprint state tracker (required, gitignored)
   - `templates/CLAUDE.md` → `CLAUDE.md` — behavioral rules (optional; recommended if your toolchain auto-loads `CLAUDE.md`, e.g. Claude Code, Cursor)
4. **Fill in every placeholder** in `AGENTS.md`. Never commit `[bracketed]` text. `AGENTS.md` is committed — keep machine-specific paths and personal tooling notes out of it. Ask the user when a placeholder has no obvious answer.
5. **Write `SPEC.md`** — product vision, versioning strategy, numbered FRs (FR-001, FR-002, …). The user writes or approves every line; AI never writes specs unilaterally. Commit it.
6. **Set up the repo:** `src/`, `test/`, build and test scripts (e.g. `package.json` or equivalent). Create an initial regression suite (even if empty) before any feature code.
7. **Point your AI tool at `AGENTS.md`** using your toolchain's convention — every agent reads it at spawn. Reference this repo in `AGENTS.md` so the SOP version is traceable.
8. **Every session after setup** begins with the **Session Start Protocol**.

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
| **Config** | `AGENTS.md` (repo root, committed) | AI + User | Machine-readable project config — roles, technical notes, git workflow. Read by every agent at spawn. Stable across a sprint. |
| **Spec** | `SPEC.md` (repo root, committed) | User | Source of truth — WHAT to build. Numbered requirements (FR-001, FR-002, …). Never derived from code. |
| **Design** | `specs/YYYY-MM-DD-<topic>-design.md` (committed) | AI + User | HOW to build a specific feature. Per sprint, user-approved before implementation. |
| **Plan** | `plans/YYYY-MM-DD-<topic>.md` (committed) | AI | Step-by-step task breakdown with complexity classification. References the design doc. |
| **State** | `SPRINT.md` (repo root, **gitignored**) | AI | Live sprint tracker. Updated at every gate. Single source of truth for "where are we." |
| **Tests** | `test/` (committed) | AI (Test Designer) | Verify spec requirements. Written from FRs before implementation. Traceable to an FR or invariant. |
| **Code** | `src/` (committed) | AI (Developer) | Implementation. Must match plan → design → spec. |

**Everything is committed except `SPRINT.md`.** The spec is the source of truth, so it gets the same change control as the code it governs: history, diffable amendments, recoverability. `SPRINT.md` alone is gitignored — it is ephemeral working memory, reset at every sprint end.

---

## The Five Roles

One agent per role per task. Roles never mix.

| # | Role | Phase | Tier | Does | Does NOT |
|---|------|-------|------|------|----------|
| 1 | **Planner** | Sprint start; spec amendments | standard | Reads spec + open issues. Proposes build order. Flags spec conflicts. Drafts spec amendments when escalated. | Write code or tests. Approve amendments — that is the user. |
| 2 | **Test Designer** | Before implementation | frontier | Reads the FR or issue. Writes failing test assertions. Commits the test file before Developer starts. | Write implementation code. Decide whether the test correctly captures the FR — that is the Spec Reviewer's pre-review. |
| 3 | **Developer** | Implementation | per task | Receives plan + pre-reviewed tests. Implements until tests pass. Opens PR. Escalates spec ambiguity instead of guessing. | Merge. Interpret requirements. Modify tests to make them pass — escalate inconsistency instead. |
| 4 | **Spec Reviewer** | (a) before Developer starts, (b) after each task | per task | **Pre-review:** checks the tests against the FR; approves or returns findings to Test Designer. **Post-review:** reads the actual code line-by-line against the spec — nothing missing, nothing extra, no misinterpretation, correct behaviour. Every finding cites file:line; a "looks good" verdict without reading the code is invalid. | Comment on code style, structure, or naming — that is the Code Reviewer. Decide what the spec should say. |
| 5 | **Code Reviewer** | After Spec Reviewer approves | per task | Reviews code quality, architecture, test coverage. Returns Critical / Important / Minor findings. | Second-guess spec requirements. Approve code the Spec Reviewer rejected. Run in parallel with Spec Reviewer. |

The **release smoke check** is a human gate, not an agent role — see Human Gates.

### Model tiers

Roles use capability tiers, not model names: **fast / standard / frontier**. The plan doc classifies every task; the classification picks the tier. Unclassified tasks default to standard.

| Task type | Developer | Reviewers |
|-----------|-----------|-----------|
| Mechanical (1–2 files, clear spec) | fast | fast |
| Standard (multi-file, some judgment) | standard | standard |
| Architectural (design decisions, broad impact) | frontier | standard |

The Test Designer is always frontier — it has the least constrained reasoning task in the pipeline, and mis-specified tests are worse than no tests. Skip the Test Designer entirely for `chore:`, `docs:`, and `ci:` commits; it applies only where behaviour changes.

> Tier mapping as of 2026-06: fast = Haiku, standard = Sonnet, frontier = Opus/Fable. Update the mapping as models evolve; the tier semantics don't change.

### Independence rules

- Developer never sees reviewer reasoning — fresh context per role, findings documents only (see Review Rejection Loop).
- Spec Reviewer always runs before Code Reviewer, never in parallel.
- **Limit:** independence reduces context bleed, not pattern-matching bias. Two same-tier agents share training and will make correlated errors. Mitigations: use different tiers across roles when budget allows; treat the human gates as the real backstop, not agent independence.

---

## Session Start Protocol

**Every session begins here — before any other action.**

1. Read `AGENTS.md` for the project.
2. Read `SPRINT.md` in the project root.
3. Report current position in one sentence: *"We are at [gate], [what is done], next step is [X]."*
4. Continue from the first incomplete gate. Do not restart completed steps.

Gate states: `⬜` open · `✅` complete · `⏳ awaiting user`. If `SPRINT.md` shows **Idle**, ask the user what to work on. If a sprint is **in progress** with no open blockers, continue without asking — the checklist is the instruction. If a gate is `⏳ awaiting user`, surface what is needed and stop.

`SPRINT.md` is the single source of truth for project position. Mark each gate in `SPRINT.md` before moving to the next. Never batch updates. `AGENTS.md` is stable config; it is not edited mid-sprint.

---

## Lifecycle

### Sprint Flow

Features and bug fixes follow the same flow; bug-fix variants in parentheses.

```
User: "I want X"   (bug fix: issue opened — symptom, root cause, repro case, fix suggestion)
  ↓
Planner  →  reads spec + issues, proposes build order
  ↓
Design phase  →  design doc  →  user approves   (bug fix: skip — the issue is the design)
  ↓
Planning phase  →  plan doc, tasks classified by complexity  →  user approves
  ↓
Test Designer  →  writes failing tests from FRs  (bug fix: test reproducing the bug)  →  commits
  ↓
Spec Reviewer (pre)  →  tests match the FR/issue? ✅ or findings → Test Designer
  ↓
Developer  →  implements until tests pass, task by task
  ↓
Per task: Spec Reviewer (post) ✅  →  Code Reviewer ✅   (rejection → Review Rejection Loop)
  ↓
(bug fix: regenerate snapshots  →  review diff  →  commit)
  ↓
Version bump  →  build  →  PR  →  human smoke check ✅  →  merge  →  release tag  →  close issues
```

### Review Rejection Loop

When any reviewer rejects:

1. The reviewer produces a **findings document** — each finding cites file:line and the FR or quality criterion violated. The findings, not the reviewer's reasoning, are the only artifact passed back.
2. A **fresh Developer context** receives plan + tests + findings and fixes. (Pre-review rejections go back to a fresh Test Designer the same way.)
3. The **same reviewer role re-reviews** — fresh instance, full re-review of the task.
4. **Two failed cycles on the same task → stop and escalate to the user.** Repeated rejection usually signals a spec or plan defect, not a coding defect.

### Spec Amendment Protocol

Requirements crystallize through implementation. When the Developer or a reviewer finds the spec ambiguous, incomplete, or contradictory:

1. **Developer pauses.** Implementation halts at the current task. No guessing.
2. **Open a spec issue.** Title: `spec: clarify FR-xxx — <one-line summary>`. Body: the ambiguity, candidate interpretations, why it matters now.
3. **Planner drafts the amendment.** Proposes a revised FR or a new one. References the original.
4. **User approves the diff.** Spec amendments are user-gated. AI does not approve spec changes.
5. **`SPEC.md` updated in a commit** (`docs(spec): revise FR-xxx — see issue #N`). The affected FR carries a `Revised YYYY-MM-DD — see issue #N` line. The commit diff is the change record.
6. **Tests revisited.** Test Designer re-evaluates the test file; Spec Reviewer pre-reviews again.
7. **Developer resumes.**

Amendments are normal, not exceptional. Silent reinterpretation is the failure mode this protocol prevents.

### Human Gates

Agents stop at these gates, mark them `⏳ awaiting user` in `SPRINT.md`, and do not continue until the user responds. Everything else agents complete autonomously.

| Gate | Who approves |
|------|--------------|
| `SPEC.md` content & amendments | Human (always) |
| Design doc | Human |
| Plan doc | Human |
| Spec Reviewer / Code Reviewer ✅ | AI |
| Version bump type | Human |
| Release smoke check | Human |
| PR merge | Human |
| Release tag | Human |

Spec gaps discovered mid-implementation follow the Spec Amendment Protocol — stop and surface; never guess or work around.

### Release Checklist

- [ ] All tests pass (`npm test` or equivalent)
- [ ] Build succeeds from source
- [ ] Version bumped (semver: patch for bugs, minor for features, major for breaking)
- [ ] PR opened, CI passes
- [ ] **Human smoke check** — open the built artifact, walk the sprint's user-facing flows from a checklist, confirm pass/fail per item. Human gate until automated E2E coverage exists; then it becomes a CI job.
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

**Tests are never updated silently.** Snapshot updates require a deliberate command, a diff review, and a commit. The snapshot diff is the verification record. After each bug fix: regenerate snapshots, read the diff, confirm the delta matches the expected fix, commit.

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
8. **Source is authoritative.** Build artifacts are generated from `src/`, never edited. Working documents (SPEC, design, plan, AGENTS) are committed — governed intent with change history. Only `SPRINT.md` is gitignored: ephemeral state, not a deliverable.

---

## Working Rules

These rules govern agent behavior during any task, regardless of role or sprint phase. They are reproduced verbatim in `templates/CLAUDE.md` for toolchains that auto-load `CLAUDE.md`.

1. **Think before coding.** State assumptions explicitly. When a request is ambiguous, present multiple interpretations and ask — never guess.
2. **Simplest solution first.** Implement the simplest thing that could work. Do not add abstractions, flexibility, or error handling that weren't explicitly requested.
3. **Surgical changes only.** Modify only what the task requires. Adjacent improvements need explicit authorization, even when the code looks wrong.
4. **Plan, then verify.** For any multi-step task, state a brief plan with verification checkpoints up front. Define success criteria so progress is measurable.

Adapted from Andrej Karpathy's January 2026 CLAUDE.md framework.

---

## Scaling

SDD is optimised for **a single developer + AI agents on a focused project**. The document stack, git workflow, role separation (roles map to people), and testing contract scale unchanged. Known bottlenecks, one-line mitigations:

1. **Agent cost per task** → task-complexity tiering (the default — see Model tiers).
2. **Sequential post-review gates** → run post-Spec and Code Review in parallel for mechanical tasks; keep sequential when the spec verdict could change what the Code Reviewer evaluates.
3. **Human smoke check** → replace with automated E2E tests (Playwright or equivalent) once a stable URL or headless build exists; keep a short manual checklist only for flows genuinely hard to automate.
4. **Test Designer overhead on trivial tasks** → skipped for `chore:`/`docs:`/`ci:` commits (the default — see Model tiers).
5. **Flat SPEC.md** → above ~80 FRs, split into a `spec/` directory (`core.md`, `ui.md`, …) with a `spec/index.md` listing all FR numbers.
6. **No parallel implementation** → for tasks with no shared state or file overlap, dispatch parallel Developers in separate git worktrees; reviewers run per task as normal.

| Project size | Adjustment |
|---|---|
| < 50 FRs, < 5 active issues | SOP as written |
| 50–150 FRs, regular sprints | Tiered models in full effect |
| 150+ FRs, multiple areas | Split SPEC.md + parallel Developers |
| Public-facing app with URL | Automate smoke check with E2E tests |
| Team > 1 human | Roles map to people; AI agents assist each role rather than filling it |

---

## Relationship to github/spec-kit

[`github/spec-kit`](https://github.com/github/spec-kit) is GitHub's specification-driven development toolkit (May 2026). Its **Specify → Plan → Tasks → Implement** flow maps directly onto SDD's document stack:

| spec-kit | SDD equivalent |
|----------|---------------|
| Specify | `SPEC.md` (numbered FRs) |
| Plan | Design doc + Plan doc |
| Tasks | Plan doc task breakdown |
| Implement | Developer role + PR |

SDD extends spec-kit with formal role separation, a test-before-implementation gate with pre-review, two-gate post-review per task, a user-gated spec amendment protocol, a human smoke check, and scaling guidance. The two are compatible: use spec-kit tooling for the Specify phase and SDD roles for the rest of the lifecycle.

---

## Projects Using This SOP

| Project | Description |
|---------|-------------|
| [loan-tracker](https://github.com/stel1os/loan-tracker) | Zero-install browser-based mortgage tracker |

---

## License

MIT
