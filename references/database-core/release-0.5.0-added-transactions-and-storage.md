# Turso 0.5.0: added transactions and storage

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Added

- core: Enable views in MVCC mode (RJ Barman)
- core/io: Introduce POSIX fallback for io_uring ftruncate (Pekka Enberg)
- feat: accept encryption keys for attached databases via URI params (Glauber Costa)
- core/mvcc: Implement Hekaton commit dependency tracking (Pere Diaz Bou)
- Implement named savepoints (Preston Thorpe)
- Strengthen support for attach databases (Glauber Costa)
- fix: introduce `changes` support to MVCC and centralize its emission in one place (Pedro Muniz)
- mark transactions as supported in compat.md (Glauber Costa)
- core/mvcc: record destroyed_tables when insert+delete occurs in same checkpoint (Pere Diaz Bou)
- core/mvcc: refresh schema root pages with newly allocated btree pages (Pere Diaz Bou)
- Add `vacuum_into` op implementation (Avinash Sajjanshetty)
- fix: introduce bounds checking in `cell_interior_read_left_child_page` (Avinash Sajjanshetty)
- Reintroduce record header caching (Jussi Saurio)
- core/mvcc: introduce savepoints to mvcc with `begin/end_statment` (Pere Diaz Bou)
- Restore record header caching (Jussi Saurio"' from Jussi Saurio)
- Reintroduce record header caching (Jussi Saurio)
- Reintroduce record header caching (Jussi Saurio)
- add simple strategy to restart WAL log when possible (Nikita Sivukhin)
- core/mvcc: Introduce support for synchronous off mode (Pekka Enberg)
