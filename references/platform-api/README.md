# Turso Platform API

```mermaid
---
config:
  flowchart:
    wrappingWidth: 400
---
flowchart LR
    brand["KAIZEN<br/>SKILL LIBRARY"]:::brand
    skill("Turso Platform API"):::skill
    first(["Resource Provisioning"]):::topic
    second(["Access and Identity"]):::topic
    third(["Organization Operations"]):::accent
    brand --- skill
    skill --- first
    skill --- second
    skill --- third
    classDef brand fill:#0f172a,color:#67e8f9,stroke:#22d3ee,stroke-width:2px,font-family:monospace
    classDef skill fill:#172554,color:#eff6ff,stroke:#60a5fa,stroke-width:2px,font-size:20px
    classDef topic fill:#0c4a6e,color:#bae6fd,stroke:#38bdf8,stroke-width:1px
    classDef accent fill:#1e293b,color:#fcd34d,stroke:#f59e0b,stroke-width:1px
    linkStyle default stroke:#38bdf8,stroke-width:1px
```

Turso Platform API references for provisioning and administering hosted database resources. Endpoint families keep request fields, response contracts and authentication requirements close to the resource operation they describe.

Use when calling the Turso Platform API for authentication and scoped tokens, audit logs, database and group lifecycle, configuration, SQL credentials, SQLite uploads, locations, organizations, v2 invites, members, invoices, subscriptions, plans, usage, response codes, TypeScript provisioning or current user details. Preserve exact HTTP methods, endpoint versions, hostnames and token roles.

## What this skill helps with

| Task | Useful reference material |
| --- | --- |
| [Provision databases](database-lifecycle-api-workflows.md) | Find database lifecycle operations and their credential contracts. |
| [Manage platform identity](api-quickstart-tokens-and-user.md) | Check platform token and current-user endpoints. |
| [Administer organizations](organization-membership-and-billing-api.md) | Inspect invitation and membership operations with their request and response fields. |

## The work behind the references

The API material is grouped by resource and operation, preserving exact HTTP methods, endpoint versions and token scopes alongside the interpreted prose.

## Start here

Start with [SKILL.md](SKILL.md) and follow its context routes.

Use the authentication role and endpoint version specified by the selected operation. Examples require their documented credentials, account context and resource identifiers.

## Sources and license

Skill reference documentation is interpreted content for use in open source projects. Check against [official documentation sources](https://docs.turso.tech/api-reference) when dealing with defects or issues that may come up from interpreting this skill's references.

Licensed under **AGPL-3.0**; see [LICENSE](../../LICENSE). Part of the [Agent Kaizen](https://github.com/LevyBytes/agent-kaizen) skill library.
