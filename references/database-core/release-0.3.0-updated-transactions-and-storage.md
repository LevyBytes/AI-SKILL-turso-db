# Turso 0.3.0: updated transactions and storage

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Updated

- Clear cached move_to_right_state after quick balancing (Nikita Sivukhin)
- core/storage: Store schema cookie cache in Pager (Pekka Enberg)
- core: move BTreeCursor under MVCC cursor (Pere Diaz Bou)
- Shared WAL lock scoping (Pedro Muniz)
- core/storage: Reduce logging level (Pekka Enberg)
- core/bree: Deduplicate BTreeCursor code (Pere Diaz Bou)
- Clear the dirty pages after pwritev fails (Pedro Muniz)
- mvcc: Make automatic checkpointing disabled by default (Pekka Enberg)
- Integrity check enhancements (Jussi Saurio)
- core/mvcc/logical-log: Move RwLock over to parking_lot (Pere Diaz Bou)
- MVCC: Perform checkpoint writes by ascending rowid (Jussi Saurio)
- core/mvcc: Exclude seek results not matching same table_id (Pere Diaz Bou)
- core/mvcc: Add automatic logical log checkpointing at commit (Pere Diaz Bou)
- Replace `dyn DatabaseStorage` with `DatabaseFile` (Pedro Muniz)
- core/wal: Validate index header at begin_write_tx (Pere Diaz Bou)
- Reject unexpected interop between WAL mode and MVCC mode (Jussi Saurio)
