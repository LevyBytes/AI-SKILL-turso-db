# Turso 0.3.0: updated functions and runtime

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Updated

- Stmt reset cursors (Nikita Sivukhin)
- core: Use FxHash for better performance (Pekka Enberg)
- Enforce strict numeric cast for op_must_be_int (bit-aloo)
- core/vdbe: Reuse the cursor for op_open_write() (Pekka Enberg)
- core: Convert RwLock&lt;Arc&gt; over to ArcSwap (Pekka Enberg)
- Vector speedup (Nikita Sivukhin)
- Place completion code in a separate file (Pedro Muniz)
- avoid unnecessary allocations (Nikita Sivukhin)
- Use `get_random` for random blob creation (Pedro Muniz)
- Use completion groups instead of io_yield_many (Pekka Enberg)
- core: Push Unsafe Send and Sync downward (Pekka Enberg)
- Conditionally increment Changes() just once (Pavan Nambi)
- make comparison case sensitive (Pavan Nambi)
- Drop cfg gating for `MAP_ANONYMOUS` (Pedro Muniz)
- Vector improvements (Nikita Sivukhin)
- Align sqlite_version() behavior with SQLite (Glauber Costa)
- Eliminate unsafe pointers (`RawSlice`) inside `RefValue` (Levy A.)
- Remove the extra `Once` requirement in Completion (Pedro Muniz)
- Rename Completion methods (Pedro Muniz)
