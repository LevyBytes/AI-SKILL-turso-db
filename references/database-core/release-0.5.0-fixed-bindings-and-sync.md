# Turso 0.5.0: fixed bindings and sync

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Fixed

- Fix sync replay generator (Nikita Sivukhin)
- sqlite3: Repair segfault in sqlite3_free_table() (Pekka Enberg)
- Normalize error codes in LibsqlError for serverless package (Pekka Enberg)
- bindings/go: Repair spurious test failures caused by port conflicts (Pekka Enberg)
- Rn locking fix (Nikita Sivukhin)
- Rn fixes 2 (Nikita Sivukhin)
- Align JavaScript driver error message with libSQL (Pekka Enberg)
- Repair bug for empty namespace in go sync driver (Preston Thorpe)
- Correct namespace passing into config in Go sync driver (Preston Thorpe)
- Fix broken encryption SDK tests (Avinash Sajjanshetty)
- repair sync-wasm package handling when no url is provided (Nikita Sivukhin)
- turso-test-runner: reduce flakiness with Rust bindings + MVCC combo (Jussi Saurio)
- sdk-kit: Expose busy snapshot error to callers (Pekka Enberg)
- bindings/rust: return error for out of bounds Row::get access (Jussi Saurio)
