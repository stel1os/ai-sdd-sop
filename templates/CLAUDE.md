# CLAUDE.md — [Project Name]

This file is loaded automatically at session start by Claude Code. It defines **how** you work in this repo. After loading, read the other two files in order:

1. **AGENTS.md** — what this project is (roles, workflow, build/test, invariants)
2. **SPRINT.md** — where we are right now (current sprint state and gates)

Reference: https://github.com/stel1os/ai-sdd-sop

---

## Working rules

These four rules govern every action you take in this repo. They apply regardless of role or sprint phase.

1. **Think before coding.** State assumptions explicitly. When a request is ambiguous, present multiple interpretations and ask — never guess.
2. **Simplest solution first.** Implement the simplest thing that could work. Do not add abstractions, flexibility, or error handling that weren't explicitly requested.
3. **Surgical changes only.** Modify only what the task requires. Adjacent improvements need explicit authorization, even when the code looks wrong.
4. **Plan, then verify.** For any multi-step task, state a brief plan with verification checkpoints up front. Define success criteria so progress is measurable.

---

## Escalation

If the spec is ambiguous, incomplete, or contradicts itself, use the **Spec Amendment Protocol** in `AGENTS.md`. Pause, open a spec issue, wait for user approval. Never silently reinterpret requirements.

---

## Reference

- `AGENTS.md` — roles, workflow, build/test, project specifics
- `SPRINT.md` — current sprint state (read every session)
- SOP: https://github.com/stel1os/ai-sdd-sop

---

Customize this file only to add project-wide **behavioral** rules. Project facts go in `AGENTS.md`. Sprint state goes in `SPRINT.md`.

The four rules above are adapted from Andrej Karpathy's January 2026 CLAUDE.md framework (Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution).
