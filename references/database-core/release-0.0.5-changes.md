# Turso 0.0.5: changes

Open when checking these changes in Turso 0.0.5, released 2024-11-18. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.5-index.md)

## Added

- `CREATE TABLE` support (Pere Diaz Bou)
- Expose Database.prepare() and Statement.all() in Wasm bindings (Pekka Enberg)
- WAL improvements (Pere Diaz Bou)
- Add primary key index scans and single-column secondary index scans (Jussi Saurio)
- `GROUP BY` support (Jussi Saurio)
- Overflow page support (Pere Diaz Bou)
- Enhance Python bindings (Jean Arhancet and Lauri Virtanen)
- Improve scalar function support (Lauri Virtanen)
## Fixed

- Fix codegen panic with `COUNT(*)` (Jussi Saurio)
- Make `LIKE` case insensitive (RJ Barman)
