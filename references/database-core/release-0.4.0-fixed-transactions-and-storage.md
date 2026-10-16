# Turso 0.4.0: fixed transactions and storage

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Fixed

- integrity check: avoid errors when the pending byte page is never used (Pedro Muniz)
- fix(storage): improve the truncated database files error message (Srinivas A)
- fix/pager&wal: require the wal write lock while rolling back frame_cache (Jussi Saurio)
- core/mvcc/logical_log: fix off-by-one reading of encrypted logical log (Pere Diaz Bou)
- Fix the io_uring squeue overflow issue (Preston Thorpe)
- Read only fixes (Pedro Muniz)
- fix/core: fix transaction issues (Jussi Saurio)
- core/storage: fix the commit path and `io_uring` issues (Preston Thorpe)
- Fix: refresh schema when a DDL commit succeeds but checkpoint fails (Jussi Saurio)
- Fix race in WAL frame_cache updates under io_uring (Mikaël Francoeur)
- fix/core: separate autocheckpoint result from transaction durability (Jussi Saurio)
- Correct StreamingWalReader behavior for checksums on uncommitted frames (Preston Thorpe)
- Fix the ignored completion in free_page (Preston Thorpe)
- Fix additional cases where pages were marked dirty after modification (Jussi Saurio)
- fix/pager: mark the freelist trunk page dirty BEFORE changing it (Jussi Saurio)
- fix/btree: turn off move_to_rightmost optimization when triggers are involved (Jussi Saurio)
- fix/mvcc: seek() must consult both mv store and btree (Jussi Saurio)
- fix/mvcc: reuse the existing schema object during mvcc bootstrap (Jussi Saurio)
- Mvcc bugfixes (Jussi Saurio)
- core/mvcc: make `exists` fall back to `BTreeCursor` (Pere Diaz Bou)
