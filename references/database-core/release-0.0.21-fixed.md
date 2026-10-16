# Turso 0.0.21: fixed

Open when checking these changes in Turso 0.0.21, released 2025-05-28. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.21-index.md)

## Fixed

- Fix LIMIT handling (Jussi Saurio)
- Correct max_frame off-by-one after WAL load (Jussi Saurio)
- btree: stop infinite looping during backwards iteration of btree table (Jussi Saurio)
- Stop labeler from labeling everything as Extensions-Other (Jussi Saurio)
- Repair bug in op_decr_jump_zero() (Jussi Saurio)
- Page cache fixes (Pere Diaz Bou)
- cli/fix: apply default app config (Diego Reis)
- Fix labeler (Jussi Saurio)
- Improve debug build validation speed (Pere Diaz Bou)
- optimizer: repair order by removal logic (Jussi Saurio)
- Fix updating single value (Pedro Muniz)
- Autoindex fix (Pedro Muniz)
- Use temporary db in sqlite3 wal tests so later tests stop failing (Preston Thorpe)
- fix labeler file extension by using .yml instead of .yaml (Mohamed A. Salah)
- Correct primary key autoindex marked as unique (Pere Diaz Bou)
- Fix: unique contraint during auto index creation (Pedro Muniz)
