# Turso 0.3.0: fixed

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Fixed

- Correct database state rollback after sync (Nikita Sivukhin)
- Repair foreign key constraint enforcement for UNIQUE indexes (Jussi Saurio)
- bindings/javascript: Improve open error messages (Pekka Enberg)
- core/storage: Fix WAL already enabled issue (Pekka Enberg)
- Return better syntax error messages (Diego Reis)
- core/vdbe: Make ALTER COLUMN propagate constraints to other table references (Preston Thorpe)
- core/translate: Make ALTER COLUMN propagate other constraint references (Preston Thorpe)
- core/translate: Make ALTER COLUMN propagate other constraint references (Preston Thorpe)
- Run deferred FK violations check correctly before committing to WAL (Jussi Saurio)
- translate/select: Correct Rowid expression rewrite when joined table refs contain no btree table (Preston Thorpe)
- Raise parse error for CHECK constraint in create table (Preston Thorpe)
- Fix: Set autocommit to true when rolling back tx on Error (Jussi Saurio)
- Fix: Make outer CTEs visible inside subqueries (Jussi Saurio)
- Fix change counter incrementation (Jussi Saurio)
- Fix another translation panic saying "should have been rewritten" (Jussi Saurio)
- Simulator: Correct alter table shadowing so it modifies index column name (Pedro Muniz)
- fix backwards compatible rowid alias behaviour (Pedro Muniz)
- Correct typo in manual.md (Yevhen Kostryka)
- core/vdbe: Add context to IdxDelete error messages (Pekka Enberg)
- Correct reserved-prefix rejection in ALTER TABLE RENAME TO (xmchx)
- Stop using an equality constraint twice for index seek (Jussi Saurio)
- Correct IN operator handling for NULL (Diego Reis)
- Clean up Simulator and correct Column constraints in sql generation (Pedro Muniz)
- Fix rusqlite compatibility claim (Dave Warnock)
- Repair op_destroy re-entrancy for DROP TABLE usage (Jussi Saurio)
- Fix VDBE program abort (Nikita Sivukhin)
- Resolve attach I/O error for in-memory databases (Preston Thorpe)
- core/incremental: Correct same-key data re-insertion (Glauber Costa)
- core/io/unix: Handle short writes in try_pwritev_raw() (FamHaggs)
