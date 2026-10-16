# Turso 0.3.0: updated testing and builds

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Updated

- antithesis: In GitHub Actions workflow, upload the config image (Pekka Enberg)
- perf/throughput: Improve reproducibility (Pekka Enberg)
- Make Nyrkio p-value stricter at 0.00001 (Henrik Ingo)
- perf/throughput: Give each rusqlite benchmark transaction its own connection (Pekka Enberg)
- github: Move fuzz tests into a separate workflow (Pekka Enberg)
- tests: Split integration tests from fuzz tests (Pekka Enberg)
- core/io: Make random generation deterministically simulated (Pedro Muniz)
- tests/integration: Reduce collation fuzz test iterations (Pekka Enberg)
- Drop views.test cases that alter testing.db (Preston Thorpe)
- tests/integration: Disable rowid alias differential fuzz test case (Pekka Enberg)
- stress: Stop threads from exiting while holding write lock (Jussi Saurio)
- After a stress test run, execute SQLite integrity check (Pedro Muniz)
- tests/fuzz: Allow all fuzz tests to read SEED env var (Preston Thorpe)
- perf/throughput: Force sqlite onto fullfsync (Pedro Muniz)
- Loosen the vector test check (Nikita Sivukhin)
- Run simulator under Miri (Bob Peterson)
- Import workspace crates by name instead of path (Pedro Muniz)
- Simulator: `Drop Index` (Pedro Muniz)
- Simulator: Keep files in sim memory IO for integrity check (Pedro Muniz)
- Simulator: `ALTER TABLE` (Pedro Muniz)
- Put all checksum tests behind the feature flag (Avinash Sajjanshetty)
- Nyrkiö nightly: Set frequency to 1 per 24h (Henrik Ingo)
- Simulator: Skip `Property::AllTableHaveExpectedContent` during stats counting (Pedro Muniz)
- Avoid repeated table name output in simulator (bit-aloo)
- perf/throughput: Remove database before benchmark run (Pekka Enberg)
- Adjust Interactions Generation so it only creates possible queries (Pedro Muniz)
- Simulator diff print (Pedro Muniz)
- Improve simulator cli (bit-aloo)
