# Turso 0.6.0: fixed transactions and storage

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Fixed

- Fix MVCC lock leaks (Jussi Saurio)
- core/mvcc: fix stale MVCC checkpoint bounds (Avinash Sajjanshetty)
- core/mvcc: fix MVCC insert preserving stale B-tree cursor residency (Avinash Sajjanshetty)
- core/storage: Fix savepoint rollback under cache pressure (Pedro Muniz)
- core/storage: Correct WAL savepoint rollback on a stale snapshot (Pedro Muniz)
- core/storage: Correct pager panic with AFTER INSERT trigger (Jussi Saurio)
- core/mvcc: Correct pager read lock panics after BEGIN DEFERRED then BEGIN CONCURRENT (Artur Kantorczyk)
- core/storage: correct WAL savepoint rollback corruption after cache spill (Ben Zaid)
- Avoid MVCC integrity_check false positives caused by stale dropped-root bookkeeping colliding with live schema rootpages (Preston Thorpe)
- vdbe: Fix bug blocking pager IO incorrect/duplicate instance (Preston Thorpe)
- Correct deferred FK mismatch on savepoint release (Pedro Muniz)
- MVCC stale row version fix during Checkpoint (Pedro Muniz)
- core/storage: Correct savepoint rollback for dirty pages (ongyimeng)
- core/pragma: correct busy_timeout being treated incorrectly as write transaction (Preston Thorpe)
- core/mvcc: Correct abandoned transactions leaving their effects behind (Avinash Sajjanshetty)
- correct global atomic mixup in buffer pool (Preston Thorpe)
- core/mvcc: Fix speculative delete conflict (Avinash Sajjanshetty)
- fix/mvcc: handle NullRow in MVCC cursors (Jussi Saurio)
- core/pragma: clear dirty pages after page_size changes and correct journal_mode handling (Preston Thorpe)
- core/storage: Fix stale b-tree count cache (Glauber Costa)
- fix: make savepoint rollback of DDL roll back instead of causing I/O error (Preston Thorpe)
