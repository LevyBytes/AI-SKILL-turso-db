# Turso 0.1.4: updated testing and builds

Open when checking these changes in Turso 0.1.4, released 2025-08-20. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.4-index.md)

## Updated

- Enable FaultyQuery by default (Pedro Muniz)
- sim: run compound selects and complex joins less often (Jussi Saurio)
- Turn off unused variables in cargo clippy for CI (Pedro Muniz)
- Add mobibench instructions to PERF.md (Preston Thorpe)
- Have Simulator delete files following a successful run (Pedro Muniz)
- bench/insert: switch to PRAGMA synchronous=full (Jussi Saurio)
- bench/insert: set locking_mode EXCLUSIVE and journal_mode=WAL for sqlite (Jussi Saurio)
- Make Sqlite parse schema during connection benchmark (Levy A.)
- Omit invalid inputs in cosine distance prop test (bit-aloo)
