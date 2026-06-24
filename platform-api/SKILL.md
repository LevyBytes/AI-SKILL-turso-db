---
name: platform-api
description: Use when calling the Turso Platform API for authentication, databases, groups, locations, organizations, invites, members, plans, usage, API tokens, response codes, and HTTP endpoints.
---

# Turso Platform API Reference

Faithful reference for Turso Platform API, split into one focused file per subject across 8 reference files.
Identifiers (API/type/function names, flags, paths, enums, numbers) are preserved verbatim.

## When to use this

Use this skill when the task involves Turso Platform API authentication, database endpoints, group endpoints, location endpoints, organization endpoints, invite and member management, plans, usage, API tokens, response codes, and HTTP API workflows.

## Workflow

1. Open `references/INDEX.md` (or `references/topics.json`) and pick the one file that matches.
2. Read only that file; open another only if the task spans subjects. Grep across files when needed: `rg -n "PATTERN" references/*.md`.
3. Treat every identifier as an exact reference fact — never invent or paraphrase.

## Gotchas

Recurring failure modes and what to do instead live in the sibling [GOTCHA.md](GOTCHA.md).

## References

All depth lives in `references/` — start at `references/INDEX.md`, metadata in `references/topics.json`. One subject per file.

## Source

Upstream: https://docs.turso.tech/

## Verification

```powershell
python .agents/skills/skill-drafting/scripts/validate_skill_package.py skills/turso-db/platform-api
```
