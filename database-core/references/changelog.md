# Changelog (part 1)

> Database Core reference. Original prose; identifiers preserved verbatim.

[tursodatabase](https://docs.turso.tech/tursodatabase)  / ** [turso](https://docs.turso.tech/tursodatabase/turso) ** Public
-  [Notifications](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fturso) Sign in before adjusting notification settings
-  [Fork 1k](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fturso)
-   [Star  20.5k](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fturso)

### Expand file tree

/

### CHANGELOG.md

Copy pathMore file actionsMore file actions

### Latest commit

### History

[History](https://docs.turso.tech/tursodatabase/turso/commits/main/CHANGELOG.md)History3551 lines (3286 loc) 204 KB/

### CHANGELOG.md

Copy path

### File metadata and controls

-
-
-
GitHub reports this changelog as 3551 lines, 3286 loc, and 204 KB. [Raw](https://github.com/tursodatabase/turso/raw/refs/heads/main/CHANGELOG.md) The page also exposes copy raw fileDownload raw fileOutlineEdit controls and raw actions.

### Changelog

### 0.6.0 -- 2026-05-14

### Added

- include a SQLite test covering the xfer optimization bug (Pavan Nambi)
- Add a regression case for the SQLite xfer optimization bug (Pavan Nambi)
- core/mvcc: Add a regression check for DESC index scans during concurrent insert (Pekka Enberg)
- give create index benchmark functions proper benchmark names (Pedro Muniz)
- bindings/rust: Support dynamic auth tokens in the sync engine (Nikita Sivukhin)
- Make `WITHOUT ROWID` support experimental (Pekka Enberg)
- add dbtest paper! (Alperen Keleş)
- testing/sqltests: Add a regression case for a self-referential trigger following ALTER TABLE RENAME (Pekka Enberg)
- testing: Add MVCC update regression coverage for a btree-only row (Pekka Enberg)
- testing: Add a recovery-heavy Whopper profile for MVCC (MikaÃ«l Francoeur")
- testing: Add a recovery-focused Whopper profile for MVCC (MikaÃ«l Francoeur)
- sqlite3: add implementations for sqlite3_stricmp and sqlite3_complete (Lucas Schwalm Silva)
- route FK NEW-image key reads through ColumnLayout (Mikaël Francoeur)
- Add regression coverage for a pager panic after savepoint rollback with overflow (Jussi Saurio)
- Add support for bind parameters in the Database API (Pekka Enberg)
- testing/sqltests: Add regression coverage for the "index cursor should have a record" bug (Jussi Saurio)
- tests/integration: Add a regression case for the `VACUUM INTO` panic (Jussi Saurio)
- testing: Add regression coverage for a pragma_table_list() panic (Jussi Saurio)
- add coverage for table-valued functions (MikaÃ«l Francoeur)
- Add savepoint rollback DST tests and repair rollback cache invalidation (Yassine Elyaakoub)
- add a stack-usage measurement tool (Pedro Muniz)
- Implement PRAGMA foreign_key_list (Mikaël Francoeur)
- core: Add the first `vaccum` op support (Avinash Sajjanshetty)
- concurrent-simulator: Add configuration for a coverage runner (Pekka Enberg)
- core/translate: Add support for `UPDATE FROM` (Pekka Enberg)
- core: support non-MVCC WITHOUT ROWID tables (Marc-André Moreau)
- ci: add stack size workflow (Pedro Muniz)
- bindings/python: Add SQLAlchemy asyncio dialect (Akira Noda)
- Add STRUCT and UNION composite types for STRICT tables (Glauber Costa)
- Bump activesupport in /examples/react-native from 7.2.3 to 7.2.3.1 (app/dependabot)
- Add an examples entry for Go driver sync (Preston Thorpe)
- core/connection: add a small helper for detecting whether db is in-memory (Avinash Sajjanshetty)
- add some sqltests (Pavan Nambi)
- bindings/python: Add named parameter support (Akira Noda)
- docs: add multiprocess access details to sql-reference (Preston Thorpe)
- fossier: add bot_policy = allow so dependabot is not blocked (Preston Thorpe)
- Bump addressable in /examples/react-native from 2.8.8 to 2.9.0 (app/dependabot)
- add a checkpoint flag for `perf/memory` (Pedro Muniz)
- core: Implement the first multi-process database access support (Preston Thorpe)
- Introduce the BTree constructor together with an RAII mutation guard (MikaÃ«l Francoeur)
- add the series blob profile under `perf/memory` (Pedro Muniz)
- Add CREATE DOMAIN / DROP DOMAIN support (Glauber Costa)
- Add a README for the turso npm package (Glauber Costa)
- Add support for CREATE TABLE AS SELECT (Glauber Costa)
- serverless/javascript: Interactive transaction support (Pekka Enberg)
- Fix promise API encryption cipher mapping and add tests (Glauber Costa)
- fix: handle bracket-quoted JSON path keys in json_extract (Srinivas A)
- Implement Temporary tables (Preston Thorpe)
- Add tests confirming canonical schema storage for attached db (Avinash Sajjanshetty)
- cli: add sqlite-backed sqltests support for .schema and .tables (Rupesh Harode)
- Add Turso CLI npm packaging for `npx turso` (Glauber Costa)
- fix: enforce FK constraints for columns introduced through ALTER TABLE ADD COLUMN (Harin)
- Add SQLite TCL conformance tests into CI (Pekka Enberg)
- core/translate: Support compound SELECT inside WHERE-clause subqueries (Pekka Enberg)
- bindings/rust: Allow Builder to receive a custom IO implementation (Hugues Morisset)
- fix: implement .dbconfig dqs_dml for controlling double-quoted string fallback (Srinivas A)
- bindings/tcl: Add a prepared statement cache plus Tcl variable binding (Pekka Enberg)
- sqlite3: Implement `sqlite3_stmt_status` (Pedro Muniz)
- sqlite3: Implement `sqlite3_stmt_readonly` (Pedro Muniz)
- sqlite3: Implement `sqlite3_progress_handler` + `sqlite3_interrupt` (Pedro Muniz)
- add the top 30 current contributors to the vouch file (Preston Thorpe)
- bindings/react-native: Add an iOS invalidate hook (Nikita Sivukhin)
- Add a new core benchmark for triggers (Preston Thorpe)
- core: Add read-only handling for the `locking_mode` pragma (Pekka Enberg)
- core/connection: Improve `ATTACH` so fresh databases initialize correctly (Avinash Sajjanshetty)
- core: Add unistr_quote(X) scalar function (Edward)
- core/translate: Add emit_table_column for reading transparent virtual columns (Glauber Costa)
- Add TPC-C benchmark (Preston Thorpe)
- core: Implement PRAGMA full_column_names and short_column_names (Pekka Enberg)
- Add a script for removing old RC versions from PyPI (Pekka Enberg)
- Add statement origin tracking (Avinash Sajjanshetty)
- Sync add column idempotent replay (Nikita Sivukhin)
- bindings/javascript: Add query timeout support per connection and per statement (Pekka Enberg)
- Add additional generated columns tests (MikaÃ«l Francoeur)
- serverless: add AbortSignal-based query timeout support (Pekka Enberg)
- core/translate: Implement ORDER BY NULLS FIRST/LAST (Glauber Costa)
- simulator: add yield injections ðŸ’‰ for mvcc at safe resumable boundaries (Avinash Sajjanshetty)
- sqlite3: Add Diesel-required C API functions (phase 2: value lifecycle) (João Lucas)
- Add .NET support information to README.md (Amodh Dhakal)
- testing/sqltests: Add named window function test cases (Mikaël Francoeur)
- Implement ALTER TABLE handling for virtual columns (MikaÃ«l Francoeur)
- add unistr(X) scalar function (Edward)
- core/mvcc: add result to on_checkpoint_end (Pere Diaz Bou)
- implement generated columns (Mikaël Francoeur)
- Add strict-table coverage for VACUUM INTO tests (Harin").)
- Support ROW_NUMBER() (Kumar)
- Support ROW_NUMBER() (Kumar Ujjawal").)
- Add a test ensuring compaction after vaccum into (Edgar Latorre)
- sqlite3: add cache=shared support for named in-memory URI databases (JoÃ£o Lucas)
- Add DEFAULT keyword support in INSERT VALUES expressions (Glauber Costa)
- core: implement ORDER BY for compound SELECT statements (Glauber Costa)
- Add Linux aarch64 and macOS x86_64 to CI Python wheel build targets (Kilian Hu)
- Drop Python 3.9 support and add Python 3.14 (Kilian Hu)
- test: add integration coverage for VACUUM INTO on encrypted databases #5851 (Ratnesh Mishra)
- Support ROW_NUMBER() (Kumar Ujjawal)
- translate: implement INDEXED BY (Jussi Saurio)
- perf: Implement the Grace algorithm for hash joins to improve disk-spilling performance (Preston Thorpe)
- core/regex: Add non text data coverage for pattern matching in regexp (Vartan Babayan)
- sqlite3: implement URI filename parsing in sqlite3_open_v2 (JoÃ£o Lucas)
- refactor: Add typed enum SortComparitorType in place of heap String (Preston Thorpe)
- Add strict-table tests for VACUUM INTO (Harin)
- sqlite3: add Diesel-required C API functions (phase 1) (João Lucas)
- docs: Removed unsupported macOS mold linker instructions (Amodh Dhakal)
- add mvcc repl (Mikaël Francoeur)
- perf/core/vdbe: add setters to Register so the underlying allocation can be reused as much as possible (Jussi Saurio)
- Add a graph query benchmark using complex multijoins (Jussi Saurio)
- fix(sqlite3): avoid sqlite3_initialize aborts when a tracing subscriber is present (JoÃ£o Lucas)
- Add a quick output diffing script for AI agents (Jussi Saurio)
- translate/triggers: support subqueries in WHEN position (Preston Thorpe)
- fix: Support ON CONFLICT definitions on PRIMARY KEY and UNIQUE columns (Jussi Saurio)
- core: implement sqlite minmax optimization & unify simple agg handling & fix ordering cost model bug (Jussi Saurio)
- Add a TLA+ specification for SQLite WAL mode transactions (Pekka Enberg)
- Add shuttle tests covering MVCC scenarios (Avinash Sajjanshetty)
- optimizer: support IN (list / uncorrelated subquery) as the driver for scan+seek (Jussi Saurio)
- Add local elle run instructions (Avinash Sajjanshetty)
- Rewrite NEW/OLD inside nested trigger subqueries (Kumar Ujjawal)
- Add PRAGMA requiring UPDATE/DELETE queries to include a WHERE clause (Avinash Sajjanshetty)
- docs: add CLI reference (getting started, options, shell commands) (Glauber Costa)
- core/translate: Fix sqlite_sequence table initialization (Preston Thorpe)

### Updated
