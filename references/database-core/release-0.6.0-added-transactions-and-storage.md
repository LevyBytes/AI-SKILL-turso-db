# Turso 0.6.0: added transactions and storage

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Added

- core: Add the first `vaccum` op support (Avinash Sajjanshetty)
- core: support non-MVCC WITHOUT ROWID tables (Marc-André Moreau)
- add a checkpoint flag for `perf/memory` (Pedro Muniz)
- core: Implement the first multi-process database access support (Preston Thorpe)
- Introduce the BTree constructor together with an RAII mutation guard (Mikaël Francoeur)
- core/mvcc: add result to on_checkpoint_end (Pere Diaz Bou)
- add mvcc repl (Mikaël Francoeur)
- Add a TLA+ specification for SQLite WAL mode transactions (Pekka Enberg)
