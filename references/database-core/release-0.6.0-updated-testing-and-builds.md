# Turso 0.6.0: updated testing and builds

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Updated

- concurrent-simulator: assign a separate budget to reopen drain work (Pekka Enberg)
- concurrent-simulator: cap the reopen drain loop with max_steps (Pekka Enberg)
- refresh the discord link used by the fossier workflow (Preston Thorpe)
- core/vacuum: reenable disabled vacuum tests (Avinash Sajjanshetty)
- github: Lock nyrkio/change-detection to a commit SHA (Pekka Enberg)
- github: Lock cargo-bins/cargo-binstall to a commit SHA (Pekka Enberg)
- github: Lock PThorpe92/fossier to a commit SHA (Pekka Enberg)
- bench: create index on a large table with mvcc enabled (Pere Diaz Bou)
- sim: check table contents after a REOPEN_DATABASE fault (Ben Zaid)
- github: Move macOS runners over to Blacksmith (Pekka Enberg)
- update fossier config (Preston Thorpe)
- ci: limit the PR labeler to the main branch (Glauber Costa)
- build the example app during CI (Nikita Sivukhin)
- perf/memory: Memory usage benchmark (Pedro Muniz)
- github: add Fossier integration to reduce possible AI slop PRs (Preston Thorpe)
- testng/sqltests: refresh the window-over-grouped snapshot after aggregate ordering changed (Pekka Enberg)
- Broaden VACUUM INTO coverage for mixed and partial indexes (Kumar Ujjawal)
- simulator: roll back gracefully after a dependent txn aborts (Avinash Sajjanshetty)
- rename `testing/runner` -> `testing/sqltests` (Pedro Muniz)
- Remove odd test accepting views that reference attached dbs only in tursodb (Jussi Saurio)
- enable trigger support in the differential fuzzer (Pedro Muniz)
- Antithesis longer weekend run (Mikaël Francoeur)
- core/mvcc: Remove test_batch_writes test case (Pekka Enberg)
