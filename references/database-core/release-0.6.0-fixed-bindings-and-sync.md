# Turso 0.6.0: fixed bindings and sync

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Fixed

- docs: fix javascript documentation reference (Josh Lyon)
- Fix sync checkpoint bug (Nikita Sivukhin)
- Correct sdk-kit race that blocked multiprocess access from bindings (Preston Thorpe)
- JavaScript conformance test fixes (Pekka Enberg)
- bindings/go: Fix named parameter binding (Glauber Costa)
- serverless/javascript: Fix prepare() losing transaction baton (Glauber Costa)
- serverless/javascript: Fix empty blob decoding returning `null` (Glauber Costa)
- serverless/javascript: Correct empty blobs and falsy rowids (Glauber Costa)
- Correct Toasty compatibility issues (Glauber Costa)
- Fix npm publish workflow asset name mismatch (Glauber Costa)
- Correct Javascript examples by using `await` during execution (aryaveersr)
- sync engine: INFO -> DEBUG, turso_core: DEBUG -> INFO (Nikita Sivukhin)
- core/vdbe: restore wasm compatibility by making schema parsing re-entrant (Preston Thorpe)
- sdk-kit: Correct Database.close() failing to finalize statements (Pekka Enberg)
- bindings/tcl: Fix turso_tcl.c compilation (Pekka Enberg)
- bindings/javascript: Correct Database.close() failing to finalize statements (Pekka Enberg)
- fix(sqlite3): address use-after-free in sqlite3_value_blob (João Lucas)
