# Turso 0.2.0: fixed transactions and storage

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Fixed

- Fix MVCC drop table (Jussi Saurio)
- Correct MVCC startup infinite loop with an existing DB (Jussi Saurio)
- fix/vdbe: reset op_transaction state properly (Jussi Saurio)
- fix/mvcc: read table_id as i64 during deserialization (Jussi Saurio)
- fix/mvcc: move log offset to end of file after recovery completes (Jussi Saurio)
- Fix SQLite database file pending byte page (Pedro Muniz)
- core/vdbe: Correct BEGIN handling after the BEGIN CONCURRENT check (Pekka Enberg)
- Fix busy handler (Lâm Hoàng Phúc)
- bugfix: reset reserved space on reused pages (Avinash Sajjanshetty)
- Fix MVCC concurrency bugs (Jussi Saurio)
- Fix 3 different MVCC bugs (Jussi Saurio)
- Repair re-entrancy problem in Pager::free_page (Jussi Saurio)
- Move `divider_cell_is_overflow_cell` under debug assertions (Pedro Muniz)
- Correct SharedWalFile deadlock in multithreaded context (Jussi Saurio)
- Fix MVCC update (Jussi Saurio)
- mvcc: correct hang when CONCURRENT tx commits while non-CONCURRENT tx is active (Jussi Saurio)
- mvcc: repair two panic sources (Jussi Saurio)
- Fix MVCC rollback (Jussi Saurio)
- Apply assorted MVCC fixes (Jussi Saurio)
- core: Default to panicking on fsync() error (Pekka Enberg)
- fix(btree): advance cursor after delete replaces an interior node (Jussi Saurio)
- core/vdbe: Fix BEGIN CONCURRENT transactions (Pekka Enberg)
- Correct stale max frame handling when starting read transactions (Jussi Saurio)
- IO: handle io_uring errors correctly (Preston Thorpe)
- pager: fix incorrect freelist page count bookkeeping (Jussi Saurio)
