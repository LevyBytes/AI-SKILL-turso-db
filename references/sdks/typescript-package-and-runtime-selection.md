# TypeScript package and runtime selection

[Context index](typescript-package-and-api-selection.md)

Turso offers four TypeScript packages:

|  | `@tursodatabase/database` | `@tursodatabase/sync` | `@tursodatabase/serverless` | `@libsql/client` |
| --- | --- | --- | --- | --- |
| **Use case** | Local / embedded database | Local database + cloud sync | Remote access (servers, containers, serverless, edge) | ORM support (Drizzle, Prisma) |
| **Engine** | Turso Database (rewrite) | Turso Database (rewrite) | Turso Database | libSQL (SQLite fork) |
| **Dependencies** | Native (Node.js, WASM) | Native (Node.js) | `fetch` only — zero native deps | Requires Node.js or `/web` subpath |
| **Concurrent writes** | Yes (MVCC) | Yes (MVCC) | Planned | Not supported |
| **Sync** | — | push/pull (local-first) | — | Embedded Replicas, with write traffic routed to the cloud primary |
| **ORM support** | Drizzle (beta) | — | — | Drizzle, Prisma, and others |

 **Starting a new project?** Choose `@tursodatabase/database` for local/embedded use, `@tursodatabase/sync` when local storage also needs cloud sync, or `@tursodatabase/serverless` for applications that connect to a remote Turso Cloud database, including Node.js servers, Docker containers, serverless functions, and edge runtimes. **Using an ORM?** Select `@libsql/client`; it is production-ready and supported by Drizzle, Prisma, and other integrations. Drizzle also offers [beta support](https://orm.drizzle.team/docs/connect-turso-database) for `@tursodatabase/database` in local/embedded scenarios. Runtime support depends on the selected package and version. Native embedded bindings and fetch-based web clients have different requirements; check the package before choosing among these deployment environments:
- Node.js versions supported by the selected package
- Deno
- CloudFlare Workers
- Netlify & Vercel Edge Functions
