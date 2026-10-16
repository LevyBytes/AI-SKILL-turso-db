# Turso 0.6.0: updated bindings and sync

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Updated

- Sync engine update pull time (Nikita Sivukhin)
- bindings/rust: make experimental_index_method available on sync Builder (Mark Hinshaw)
- bindings/tcl: collect rows across statements during `db eval` (Pekka Enberg)
- bindings/javascript: Have Database.prepare() return a promise (Pekka Enberg)
- serverless/javascript: preserve zero lastInsertRowid (Akira Noda)
- Move JavaScript conformance tests (Pekka Enberg)
- Improve Turso database sync (Nikita Sivukhin)
- sqlite3: expose sqlite3_search_count through the TCL test harness (Pekka Enberg)
- bindings/javascript: use the proper TableColumn[] type instead of Promise (ericfjl)
- bindings/javascript: Have Database.prepare() return a promise (Pekka Enberg)
- Update better-sqlite3 (Pekka Enberg)
- testing/javascript: add a regression test for stale query timeout guards (Pekka Enberg)
- Enable fts support in sdk-kit (Preston Thorpe)
- bindings/rust: discard child stdio from local sync server tests (Pekka Enberg)
- bindings/tcl: Enable generated columns (Pekka Enberg)
- github: publish .Net bindings through NuGet (Amodh Dhakal)
- c-api: handle null statement ptr safely in sqlite3_reset (Preston Thorpe)
- Move Python bindings to declarative PyO3 module syntax (Kilian Hu)
- Local sync server without checkpointing (Nikita Sivukhin)
- turn off auto checkpoint for sync engine connections (Nikita Sivukhin)
- Rust Bindings: use `Cow` to remove needless String allocations (Fahd Ashour)
- turso-serverless/compat: use AsyncLock to serialize concurrent execute() calls (Pekka Enberg)
- go: use uintptr so garbage collection excludes pointers (Piotr Sarna)
- serverless: serialize concurrent execute() calls on a connection (Pekka Enberg)
- bindings/rust: use the correct Column decl_type (Preston Thorpe)
