# Turso 0.6.0: added bindings and sync

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Added

- bindings/rust: Support dynamic auth tokens in the sync engine (Nikita Sivukhin)
- sqlite3: add implementations for sqlite3_stricmp and sqlite3_complete (Lucas Schwalm Silva)
- Add support for bind parameters in the Database API (Pekka Enberg)
- bindings/python: Add SQLAlchemy asyncio dialect (Akira Noda)
- Add an examples entry for Go driver sync (Preston Thorpe)
- bindings/python: Add named parameter support (Akira Noda)
- Add a README for the turso npm package (Glauber Costa)
- serverless/javascript: Interactive transaction support (Pekka Enberg)
- Add Turso CLI npm packaging for `npx turso` (Glauber Costa)
- Add SQLite TCL conformance tests into CI (Pekka Enberg)
- bindings/rust: Allow Builder to receive a custom IO implementation (Hugues Morisset)
- bindings/tcl: Add a prepared statement cache plus Tcl variable binding (Pekka Enberg)
- sqlite3: Implement `sqlite3_stmt_status` (Pedro Muniz)
- sqlite3: Implement `sqlite3_stmt_readonly` (Pedro Muniz)
- sqlite3: Implement `sqlite3_progress_handler` + `sqlite3_interrupt` (Pedro Muniz)
- bindings/react-native: Add an iOS invalidate hook (Nikita Sivukhin)
- Add a script for removing old RC versions from PyPI (Pekka Enberg)
- Sync add column idempotent replay (Nikita Sivukhin)
- bindings/javascript: Add query timeout support per connection and per statement (Pekka Enberg)
- serverless: add AbortSignal-based query timeout support (Pekka Enberg)
- sqlite3: Add Diesel-required C API functions (phase 2: value lifecycle) (João Lucas)
- sqlite3: add cache=shared support for named in-memory URI databases (João Lucas)
- Add Linux aarch64 and macOS x86_64 to CI Python wheel build targets (Kilian Hu)
- Drop Python 3.9 support and add Python 3.14 (Kilian Hu)
- sqlite3: implement URI filename parsing in sqlite3_open_v2 (João Lucas)
- sqlite3: add Diesel-required C API functions (phase 1) (João Lucas)
- fix(sqlite3): avoid sqlite3_initialize aborts when a tracing subscriber is present (João Lucas)
