# libSQL and Turso database comparison

[Context index](database-products-and-cloud-setup.md)

## tursodatabase/libsql

libSQL began as a SQLite fork, motivated by SQLite being [open-source but not open-contribution](https://www.sqlite.org/copyright.html). It is ready for production use, remains fully backwards compatible with SQLite, and includes additions such as native vector search. libSQL was Turso's starting point. The current priority is [Turso Database](https://docs.turso.tech/tursodb/quickstart), a from-scratch SQLite rewrite built for very high database density without depending on servers or connectivity. For new work, Turso recommends [Turso Database](https://docs.turso.tech/tursodb/quickstart). Choose libSQL when a mission-critical system needs a proven base today.

### tursodatabase/libsql

Use GitHub to view the libSQL source, open issues or feature requests, and submit pull requests.

### Discord

Use Discord for conversations about libSQL development.

### libSQL vs. Turso Database

 libSQL forks SQLite while preserving the file format, API, and complete backwards compatibility. It adds capabilities the SQLite ecosystem wanted but could not upstream. [Turso Database](https://docs.turso.tech/tursodb/quickstart) takes another path: it rewrites SQLite from the ground up, preserving SQLite semantics on a modern architecture for concurrent writes, async I/O, and extremely high database density.

|  | libSQL | Turso Database |
| --- | --- | --- |
| Approach | Fork of SQLite | Full rewrite of SQLite |
| Maturity | Production-ready | Evolving (beta) |
| SQLite compatibility | Full (identical file format and API) | Backwards compatible |
| Best for | Mission-critical workloads today | New builds, agents, smart devices, high-density scenarios |

 Turso maintains both projects, and both accept open contribution. [Turso Cloud](https://docs.turso.tech/turso-cloud) hosts libSQL and also offers Turso Database in early preview. Check hosted availability separately from the embedded engine version.

### Extensions

 For vector capabilities, prefer the native [libSQL vector datatype](https://docs.turso.tech/features/ai-and-embeddings) instead of enabling vector extensions. The complete supported-extension list is available [here](https://docs.turso.tech/features/sqlite-extensions).
