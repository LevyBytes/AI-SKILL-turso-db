# Turso 0.0.22: added

Open when checking these changes in Turso 0.0.22, released 2025-06-19. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.22-index.md)

## Added

- Implement pragma wal_checkpoint() (Pedro Muniz)
- Introduce abbreviated aliases for `.quit` and `.exit` (Krishna Vishal)
- Add manual WAL sync before con.close checkpoint and repair async checkpoint bug (Preston Thorpe)
- Finish the ALTER TABLE implementation (Levy A.)
- Add affinity-based type coercion for seek and comparison operations (Krishna Vishal)
- bindings/javascript: Add pragma() support (Anton Harniakou)
- Insert sleep between write tests to prevent database locking issues (Pedro Muniz)
- bindings/java: Implement JDBC4DatabaseMetadata getTables (Kim Seon Woo)
- bindings/javascript: add Statement source property (Anton Harniakou)
- Support `sqlite_master` schema table name alias (Anton Harniakou)
- js-bindings/implement .name property (Anton Harniakou)
- bindings/java: add Linux build support (Diego Reis)
- bindings/javascript: add Statement database property (Anton Harniakou)
- simulator: include CREATE INDEX in interactions (Jussi Saurio)
- Add pragma table-valued function support (Piotr Rżysko)
- bindings/javascript: implement proper exec() method and raw() mode (Diego Reis)
- Add simulator-docker-runner to run limbo-sim repeatedly on AWS (Jussi Saurio)
- simulator: add an option that disables BugBase (Jussi Saurio)
- simulator: switch to tracing, run io.run_once, and include update queries (Pere Diaz Bou)
- Fix: initialize aggregate regs as NULL at startup (Jussi Saurio)
- Add ci stress test using 1 thread and 10k iterations (Pere Diaz Bou)
