# Turso 0.0.22: fixed

Open when checking these changes in Turso 0.0.22, released 2025-06-19. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.22-index.md)

## Fixed

- Correct lossy `Value::Blob` conversion into a utf-8 `String` (Luca Muscat)
- Make update queries set `n_changes` (Kim Seon Woo)
- bindings/rust: Fix Rows::next() I/O dispatcher handling (Pekka Enberg)
- cli: prevent panic for queries under 7 chars (Nils Koch)
- For `no such table`, return parse error rather than corrupt error (Pedro Muniz)
- simulator: turn off all ansi encodings for the debug print log file (Pedro Muniz)
- Stop large inserts into unique indexes from hanging (Jussi Saurio)
- Improve btree operation instrumentation and clean up `RefValue` debug output (Pedro Muniz)
- sim/aws: repair vibecoding errors in logic (Jussi Saurio)
- Correct OR clause handling in HAVING (Jussi Saurio)
- fix: correct placeholder label used in where clause translation (Pedro Muniz)
- Correct rowid to_sql_string (Pedro Muniz)
- Stop indexes with non-contiguous columns from being used incorrectly (Jussi Saurio)
- Refactor BTree traversal and repair bugs (Pere Diaz Bou)
- Repair `LimboRwLock` write and read locks (Pere Diaz Bou)
- fix: validate UTF-8 input so keyword_token is safe (ankit)
- Make UPDATE work on non-unique indexes (Jussi Saurio)
- Fix: keep `PRAGMA cache_size` changes scoped to the current session (meteorgan)
- sim/aws: fix sim timeout handling (Jussi Saurio)
- Fix WAL frame checksum mismatch (Diego Reis)
- Set maximum open simulator-created issues (Jussi Saurio)
- Ensure DELETE opens cursors for indexes (Jussi Saurio)
- Fix: allow DeferredSeek on multiple cursors within one program (Jussi Saurio)
- Change stress test to ignore unique constraint violation (krishna sindhur)
- Give ProgramBuilder::cursor_ref unique keys (Jussi Saurio)
- Fix `serialize()` unreachable panic (Krishna Vishal)
- Btree: update cursor record state in insert_into_page() (Jussi Saurio)
