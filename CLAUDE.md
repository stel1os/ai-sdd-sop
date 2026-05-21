# CLAUDE.md — ai-sdd-sop repo

This repo is the canonical source of the Spec-Driven Development SOP for AI-assisted projects. Changes to it are governed by the SOP itself.

## Rules for contributors (human or AI)

- **No direct commits to master.** All changes go through a branch + PR.
- **Branch naming:** `docs/vX.Y-short-description`
- **Every merge to master requires:**
  - Version bump in README header (`**Version:** vX.Y.Z — YYYY-MM-DD`)
  - `CHANGELOG.md` entry for the new version
  - GitHub release tag (`gh release create vX.Y.Z`)
- **PR description must include** what changed and why — the change control record.

## What counts as a change requiring a version bump

- Any edit to `README.md` content (roles, lifecycle, rules, testing contract, scaling)
- Any edit to `templates/AGENTS.md` or `templates/SPRINT.md`
- Structural changes (new sections, removed sections)

## What does NOT require a version bump

- Typo fixes and formatting only (use `chore:` commit prefix, no release tag needed)
- `CHANGELOG.md` updates that are part of a version bump commit

## Proposing changes

Before editing `README.md`, record the proposed change in the vault `SDD-SOP/draft-sop.md`. Batch small changes into a single version increment rather than releasing one change at a time.

## This file is not a template

`CLAUDE.md` here governs contributions to **this repo**. It is not a starter for downstream projects. Downstream projects use `templates/AGENTS.md` instead — that is the tool-agnostic project config the SOP mandates.
