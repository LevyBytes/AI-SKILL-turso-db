# Turso 0.4.0: updated testing and builds

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Updated

- test runner: Test Converter (Pedro Muniz)
- Test runner Foundation (Pedro Muniz)
- Print failure output at the end when using cargo nextest (Pedro Muniz)
- Improve turso_stress (Mikaël Francoeur)
- Change bench-profile so better flamegraphs can be generated (Jussi Saurio)
- all-mvcc: re-enable tests that work (Pere Diaz Bou)
- Adjust the tpc-h bench script for easier result comparison (Preston Thorpe)
- stress: switch to a multithreaded runtime (Jussi Saurio)
- Turn on tokio-unstable in the Antithesis image (Mikaël Francoeur)
- Antithesis observability improvements (Mikaël Francoeur)
- stress: make the SQLite integrity check clearer (Pekka Enberg)
- Put all testing dbs into WAL journal mode (Preston Thorpe)
- aws/sim: disable io-uring (Jussi Saurio)
- Clean up the core tester to parse expected select-query values correctly with `conn.execute` and `conn.exec_rows` (Pedro Muniz)
- In tests, rely only on system-library cmath for better portability (Nikita Sivukhin)
- Install sqlite locally for tests and other scripts (Pedro Muniz)
- docs: align the CONTRIBUTING.md clippy command with the CI job (Nuno Gonçalves)
- stress: Make random seed configurable (Pekka Enberg)
- whopper: Simulate time (Pekka Enberg)
- Raise the lantency check for a flaky test in test_read_path.rs (Preston Thorpe)
- Revert the index_scan_compound_key_fuzz change (Pedro Muniz)
- core/mvcc/tests: un-ignore seek tests (Pere Diaz Bou)
- CI: simulator tweaks (Jussi Saurio)
- Simulator Roadmap (Alperen Keleş)
- simulator: generate additional INSERT INTO ... SELECT self-inserts (Mikaël Francoeur)
- sim/aws: use memory IO 100% of the time and differential 50% of the time (Jussi Saurio)
- simulator: check all tables only when tables are available to check (Pedro Muniz)
- Simulator: simplify and refactor `InteractionPlan` (Pedro Muniz)
- Allow nested self-inserts in the simulator (Mikaël Francoeur)
- Correct column creation order in join tests (Pavan Nambi)
- Nyrkiö: set every comment-on value to false (Henrik Ingo)
