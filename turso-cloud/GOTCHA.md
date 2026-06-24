# Turso Cloud — Gotchas

Recurring failure modes when relying on the Turso Cloud reference, and what to do instead. Read alongside `SKILL.md`.

- Turso spans several product areas (Database, Cloud, libSQL, CLI, Platform API, SDKs, SQL) — this is a router; open the sub-skill that matches and use its `references/INDEX.md` to find the exact file.
- Turso docs change over time; verify version-sensitive CLI flags, API endpoints, SDK imports, auth scopes, quotas, and beta features against the live docs or installed toolchain before acting.
- Preserve exact identifiers including command names, endpoint paths, SDK symbols, SQL functions, PRAGMAs, enum values, flags, URLs, numbers, and file names.
- This package was built from HTML documentation pages; `api-reference/openapi.json` was reported but not ingested unless a future task explicitly asks for OpenAPI schema coverage.
