# Turso 0.1.2: added

Open when checking these changes in Turso 0.1.2, released 2025-07-15. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.2-index.md)

## Added

- Include `fuzz` in CI checks (Levy A.)
- Add async header accessor functionality (Zaid Humayun)
- core/vector: add Euclidean distance support for vector search (KarinaMilet)
- Fix: OP_NewRowId generates semi random rowid when largest rowid is `i64::MAX` (Krishna Vishal)
- vdbe: fix min() and max() issues and add ignored fuzz test (Jussi Saurio)
- github: move to newest Nyrkiö Github action (Henrik Ingo)
- Add Nyrkiö to README partners section (Henrik Ingo)
- Add except operator support for Compound select (meteorgan)
- btree: correct comparison implementation in key_exists_in_index() (Jussi Saurio)
- bindings/java: implement methods required for JetBrains Datagrip (Kim Seon Woo)
- Give property insert-values-select an interactive transaction (Pere Diaz Bou)
- Include pere in antithesis (Pere Diaz Bou)
- cli: add `.headers` command support (Pekka Enberg)
- stress: add method to run stress with indexes enabled (Jussi Saurio)
- sim: expose feature flags (indexes,mvcc) in CLI args (Jussi Saurio)
- Add JDBC4StatementTest multi select test (Kim Seon Woo)
- bindings/dart initial implementation (Andika Tanuwijaya)
- bindings/javascript: Implement Database.open (Lucas Forato)
- add `libsql_disable_wal_checkpoint` (Pedro Muniz)
- Add threshold for large page cache values (Krishna Vishal)
- Rollback schema support (Pere Diaz Bou)
- Add README for rust bindings (Glauber Costa)
- Add basic readme for typescript binding (Glauber Costa)
- Add benchmark comparing connection time with table count (Glauber Costa)
- Add new connection opening from sqlite compatible URI and read-only connections (Preston Thorpe)
- Simplify `PseudoCursor` implementation (Levy A.)
- bind/js: add expand tests (Mikaël Francoeur)
