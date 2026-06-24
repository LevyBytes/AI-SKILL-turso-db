---
name: sdks
description: Use when connecting applications to Turso with TypeScript, JavaScript, Python, Rust, Go, Java, Dart, Flutter, Kotlin, Swift, Ruby, PHP, C, ActiveRecord, SQL over HTTP, ORMs, or framework integrations.
---

# Turso SDKs Reference

Faithful reference for Turso SDKs, split into one focused file per subject across 13 reference files.
Identifiers (API/type/function names, flags, paths, enums, numbers) are preserved verbatim.

## When to use this

Use this skill when the task involves Turso SDK authentication, authorization, platform tokens, fine grained permissions, JavaScript and TypeScript, Python, Rust, Go, Java, Dart, Flutter, Kotlin, Swift, Ruby, PHP, C, ActiveRecord, SQL over HTTP, ORM setup, and framework guides.

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
python .agents/skills/skill-drafting/scripts/validate_skill_package.py skills/turso-db/sdks
```
