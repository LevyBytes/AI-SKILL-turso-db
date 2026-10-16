# Turso 0.2.0: updated testing and builds

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Updated

- CI: On every PR, run long fuzz tests and stress with 2 threads for stress (Jussi Saurio)
- Enable workflow_dispatch across all CI so jobs can be re-run (Preston Thorpe)
- simulator: Reopen database with mvcc and indexes as needed (Pedro Muniz)
- Improve throughput benchmarks (Pekka Enberg)
- Simulator: Concurrent transactions (Pedro Muniz)
- Record read/write latencies in encryption benchmarks (Avinash Sajjanshetty)
- Slightly improve stress testing tool (Nikita Sivukhin)
- core/mvcc/logical-log: Rework get log path in tests (Pere Diaz Bou)
- antithesis-tests: Change "utils.py" name to "helper_utils.py" (Pekka Enberg)
- Simulator Runtime generation (Pedro Muniz)
- Run checksum tests when checksum feature is on (Kacper Kołodziej)
- Improve Sim Snapshot isolation code (Pedro Muniz)
- whopper: Handle file size limits gracefully in simulator (Avinash Sajjanshetty)
- Turn on encryption option in Whopper (Avinash Sajjanshetty)
- Simulator Multiple Connections (Pedro Muniz)
- whopper: Handle write-write conflict (Pekka Enberg)
- Move performance workflows to GitHub runners (Diego Reis)
- test/fuzz: Improve tx isolation test maintainability/usability (Jussi Saurio)
- Make whopper execute with checksums (Avinash Sajjanshetty)
- Whopper + MVCC (Pekka Enberg)
- Commit uncommitted whopper lockfile (Jussi Saurio)
- simulator: Simplify code by using extract_if() (Pavan Nambi)
