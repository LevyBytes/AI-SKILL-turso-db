# Turso 0.2.0: fixed bindings and sync

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Fixed

- Repair sync-engine behavior when an auth token comes from a dynamic function (Nikita Sivukhin)
- Correct sync-client encryption config (Nikita Sivukhin)
- Fix JavaScript bindings (Nikita Sivukhin)
- sqlite3: Canonicalize path to correct compatibility test error (Samuel Marks)
- stress: Retry sync after errors to prevent panic, take 2 (Pekka Enberg)
- stress: Retry sync after errors to avoid panic (Pekka Enberg)
- Apply multiple sync fixes (Nikita Sivukhin)
- fix wasm-runtime package.json (Nikita Sivukhin)
- hack wasm imports around Vite and Next.js build system issues (Nikita Sivukhin)
