---
name: turso-db
description: >-
  Use when working with Turso Database, Turso Cloud, libSQL, AgentFS, Turso CLI, Turso Platform API, SDKs, SQL reference, sync, migrations, auth tokens, groups, organizations, vector search, embedded replicas, local development, SQLite compatible databases, or Turso connection code.
---

# Turso Reference

Faithful, task-routed reference for Turso, split into one focused file per subject. Identifiers are
preserved verbatim. Routes to the area sub-skills below — open the one that matches and use its
`references/INDEX.md`.

## When to use this

Use this skill when the task involves Turso Database, Turso Cloud, libSQL, AgentFS, Turso CLI commands, Turso Platform API endpoints, Turso SDK setup or reference work, SQL reference questions, sync, vector search, embedded replicas, local development, migrations, auth tokens, groups, organizations, billing, or SQLite compatible Turso database behavior.

## Routes

| Area | Use for | Open |
| --- | --- | --- |
| AgentFS | AgentFS installation, CLI reference, audit logs, copy on write overlays, MCP server integration, NFS exports, sandboxing, shared sessions, sync with Turso Cloud, and AgentFS SDKs. | `agentfs/SKILL.md` |
| Turso CLI | Turso CLI installation, authentication, headless mode, auth token commands, database commands, group commands, organization commands, plan commands, and command line help. | `cli/SKILL.md` |
| Turso Database Core | Turso Database introductions, quickstarts, libSQL, local development, Turso Database beta features, sync, AI and agent guides, vector search, change data capture, concurrent writes, encryption, and database concepts. | `database-core/SKILL.md` |
| Turso Platform API | Turso Platform API authentication, database endpoints, group endpoints, location endpoints, organization endpoints, invite and member management, plans, usage, API tokens, response codes, and HTTP API workflows. | `platform-api/SKILL.md` |
| Turso SDKs | Turso SDK authentication, authorization, platform tokens, fine grained permissions, JavaScript and TypeScript, Python, Rust, Go, Java, Dart, Flutter, Kotlin, Swift, Ruby, PHP, C, ActiveRecord, SQL over HTTP, ORM setup, and framework guides. | `sdks/SKILL.md` |
| Turso SQL Reference | SQLite compatibility, Turso SQL data types, expressions, functions, extensions, PRAGMAs, CLI shell commands, command line options, experimental SQL features, and SQL statement syntax. | `sql-reference/SKILL.md` |
| Turso Cloud | Turso Cloud overview, access allow rules, durability guarantees, BYOK encryption, private endpoints, cloud limitations, migration to Turso, managed features, branching, embedded replicas, billing, and hosting platform guides. | `turso-cloud/SKILL.md` |

## Workflow

1. Identify the area above and open that sub-skill's `SKILL.md` (or its `references/INDEX.md`).
2. Read the one reference file that matches.
3. Treat every identifier as an exact reference fact.

## Gotchas

Recurring failure modes and what to do instead live in the sibling [GOTCHA.md](GOTCHA.md).

## References

Each sub-skill carries its own `references/INDEX.md`, `references/topics.json`, and per-subject files.

## Source

Upstream: https://docs.turso.tech/

## Verification

```powershell
python .agents/skills/skill-drafting/scripts/validate_skill_package.py --package skills/turso-db
```
