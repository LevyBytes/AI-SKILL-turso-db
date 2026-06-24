---
name: agentfs
description: Use when working with AgentFS for AI agent filesystems, audit logs, copy on write overlays, MCP access, NFS access, sessions, sandboxing, sync, Python SDK, Rust SDK, or TypeScript SDK.
---

# AgentFS Reference

Faithful reference for AgentFS, split into one focused file per subject across 3 reference files.
Identifiers (API/type/function names, flags, paths, enums, numbers) are preserved verbatim.

## When to use this

Use this skill when the task involves AgentFS installation, CLI reference, audit logs, copy on write overlays, MCP server integration, NFS exports, sandboxing, shared sessions, sync with Turso Cloud, and AgentFS SDKs.

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
python .agents/skills/skill-drafting/scripts/validate_skill_package.py skills/turso-db/agentfs
```
