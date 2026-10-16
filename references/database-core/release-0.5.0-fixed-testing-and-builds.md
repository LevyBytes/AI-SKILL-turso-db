# Turso 0.5.0: fixed testing and builds

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Fixed

- concurrent-simulator: Fix DDL inside BEGIN CONCURRENT transactions (Pekka Enberg)
- Temporarily drop big sqltest to clear all the CI errors (Preston Thorpe)
- Make MVCC Elle check errors fail CI build (Pekka Enberg)
- fix: tidy db-log and repair WAL extension behavior for attached DBs in simulator (Glauber Costa)
- fix/mvcc: fix test_logical_log_header_reserved_bytes_rejected test (Jussi Saurio)
- attempt to repair js ci (Nikita Sivukhin)
- Fix broken build (Pavan Nambi)
- fix build (Mikaël Francoeur)
- fix: Loosen vector L2 distance test tolerance for simsimd and Rust precision differences (Pedro Muniz)
- Fix ruff check errors (Avinash Sajjanshetty)
- Fix Antithesis CI config (Mikaël Francoeur)
- sqlancer dockerfile fix (Jussi Saurio)
- fix docker builds (Pedro Muniz)
- fix antithesis dockerfile: download libvoidstar.so in Dockerfile instead of using COPY (Jussi Saurio)
- Run cargo clippy --fix --all in turso-test-runner (Preston Thorpe)
- Correct sqlancer-runner image name in GH action build (Preston Thorpe)
- Fix misleading turso-test-runner comments regarding mvcc&readonly (Jussi Saurio)
- Ci fixes (Jussi Saurio)
- Scalarfunc fuzzer + fixes (Jussi Saurio)
- fix: avoid creating nested aggregates in cte_fuzz (Jussi Saurio)
- Restore compilation after the unused-vars PR and integrity-check PR affected each other (Jussi Saurio)
- whopper: Handle BusySnapshot error gracefully (Pekka Enberg)
- stress: handle I/O error cleanly instead of panicking (Pekka Enberg)
- stress: avoid panic when BusySnapshot error is returned (Jussi Saurio)
- vector: raise fuzz error tolerance on windows (Jussi Saurio)
- sim: fix yet another apply_snapshot() edge case (Jussi Saurio)
