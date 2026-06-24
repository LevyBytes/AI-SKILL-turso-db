# Turso Database Reference Skill

A reference skill for **[Agent Kaizen](https://github.com/DirectorGunner/agent-kaizen)** covering the Turso documentation — Turso Database, Turso Cloud, libSQL, AgentFS, the Turso CLI, the Turso Platform API, the SDKs, and the SQL reference. The prose is original (recontextualized); identifiers — command names, endpoint paths, SDK symbols, SQL functions, PRAGMAs, flags, and URLs — are preserved exactly.

## What this skill covers

This skill is a faithful, task-routed reference for Turso: Turso Database, Turso Cloud, libSQL, AgentFS, the Turso CLI, the Turso Platform API, the SDKs, and the SQL reference. It answers questions about sync, embedded replicas, vector search, migrations, auth tokens, groups, organizations, local development, and SQLite-compatible database behavior. Because it is a router, it dispatches each request to the sub-skill for the matching area and that sub-skill's `references/INDEX.md`. Identifiers — command names, endpoint paths, SDK symbols, SQL functions, PRAGMAs, flags, and URLs — are preserved exactly.

## What's inside

- **`SKILL.md`** — the router: a Routes table mapping each Turso area to its sub-skill, plus the workflow for picking the right one.
- **Sub-skill folders**, each with its own `SKILL.md`, `references/` (right-sized topic files plus `INDEX.md` and `topics.json`), and `GOTCHA.md`:
  - `agentfs/` — AgentFS installation, CLI, audit logs, copy-on-write overlays, MCP integration, NFS exports, sandboxing, shared sessions, sync, and SDKs.
  - `cli/` — Turso CLI installation, authentication, headless mode, and the auth-token, database, group, organization, and plan commands.
  - `database-core/` — Turso Database intros, quickstarts, libSQL, local development, beta features, sync, vector search, change data capture, concurrent writes, and encryption.
  - `platform-api/` — Turso Platform API authentication, database/group/location/organization endpoints, members and invites, plans, usage, API tokens, and response codes.
  - `sdks/` — SDK authentication and authorization across JavaScript/TypeScript, Python, Rust, Go, Java, Dart/Flutter, Kotlin, Swift, Ruby, PHP, and C, plus ORM and framework guides.
  - `sql-reference/` — SQLite compatibility, data types, expressions, functions, extensions, PRAGMAs, shell commands, and SQL statement syntax.
  - `turso-cloud/` — Turso Cloud overview, access rules, durability, BYOK encryption, private endpoints, limitations, branching, embedded replicas, and billing.

## Use it

This skill is one git repo inside the Agent Kaizen **skills store**. The store nests two folders on purpose: the outer **`SKILLS\`** is a VS Code project for building and maintaining skills (its own workspace + tooling), and the inner lowercase **`skills\`** holds every skill as its own repo. That split lets a project pull skills two ways — the **whole `skills\` folder at once** (loads everything — **not recommended**) or **one skill at a time** (recommended: load only what a task needs and stay under Claude Code's skill-listing budget).

Paths below use **`%DEVROOT%`** — the `DEVROOT` environment variable pointing at the folder that contains `SKILLS\`. Set it once by running **`SetDevRoot.cmd`** in the SKILLS repo root (no admin); a new shell then resolves `%DEVROOT%` automatically.

Link **just this skill** (recommended) — a Windows directory junction, no admin:

```cmd
mklink /J .agents\skills\turso-db  "%DEVROOT%\SKILLS\skills\turso-db"
mklink /J .claude\skills\turso-db  "%DEVROOT%\SKILLS\skills\turso-db"
```

Or link the **whole store** at once (loads every skill — not recommended outside a skills-dev project):

```cmd
mklink /J .agents\skills  "%DEVROOT%\SKILLS\skills"
mklink /J .claude\skills  "%DEVROOT%\SKILLS\skills"
```

Remove a link (the store copy is untouched):

```cmd
rmdir .agents\skills\turso-db
rmdir .claude\skills\turso-db
```

The agent (OpenAI Codex, Claude Code) then auto-loads this skill whenever a task matches its triggers. Built and validated with **[skill-drafting](https://github.com/DirectorGunner/AI-skill-drafting)** to the Agent Kaizen gold standard.

## Status

Recontextualized into original prose (identifiers preserved); packaged in the skills store and ready to publish as the public `AI-SKILL-turso-db` repository. Publication is performed by the maintainer.

## Reducing idle context cost (skill invocation policy)

Every installed skill costs a little context on every session: the agent sees each skill's name and description before you ever use it. If you rarely use this skill in a given project, you can keep it installed and still explicitly invocable while hiding it from the model's automatic listing.

Doing so does **not** modify this skill's source repo — the policy lives in your local agent settings. The Agent Kaizen **[skill-drafting](https://github.com/DirectorGunner/AI-skill-drafting)** repo ships a manager, `scripts/skill_policy.py`, that sets this for all your skills at once:

```bash
python skill_policy.py audit     # list every skill + its current policy + idle cost
python skill_policy.py plan      # write a decision file with recommendations (nothing applied)
# edit that decision file: set selected_policy + approved:true for the skills you choose
python skill_policy.py preview   # show the exact change
python skill_policy.py apply     # apply ONLY what you approved (backup + rollback recorded)
python skill_policy.py restore   # roll back
```

- **Claude Code (works today):** writes `skillOverrides: { "<skill>": "user-invocable-only" }` to `.claude/settings.local.json` — zero idle listing cost, still available from the `/skills` menu. Start a new session for it to take effect; invoke it any time via `/skills`.
- **Codex (currently unreliable):** explicit-only Codex skills are affected by an open bug ([openai/codex#23454](https://github.com/openai/codex/issues/23454)) where `$skill` invocation of an explicit-only local skill can fail. Until it's fixed, leave Codex skills implicit, or fully disable rarely-used ones with `[[skills.config]] enabled = false` in `config.toml`. The manager audits Codex but does not change Codex policy.
