# Turso 0.1.5: fixed

Open when checking these changes in Turso 0.1.5, released 2025-09-10. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.5-index.md)

## Fixed

- Repair clear_page_cache method and rollback (Preston Thorpe)
- Repair read_entire_wal_dumb by building the frame cache incrementally (Preston Thorpe)
- Correct merge script to prompt while tests remain in progress (Preston Thorpe)
- SQL generation fixes (Pekka Enberg)
- Correct MakeRecord affinity handling (Pekka Enberg)
- Repair infinite loop on IO failure while allocating first page (Preston Thorpe)
- Repair Next opcode crash when cursor stack lacks pages (Jussi Saurio)
- cli: Correct dump compatibility for "PRAGMA foreign_keys" (Pekka Enberg)
- Small fixes (Nikita Sivukhin)
- Avoid allocating affinity errors and immediately fallbacking them (Jussi Saurio)
- Correct float formatting, comparison, and Blob concat (Levy A.)
- Repair infinite loop when query begins with comment token "--" (Lâm Hoàng Phúc)
- Fix sqlite3 test cases (Pekka Enberg)
- Remove simulator non-determinism (Pedro Muniz)
- Correct ImmutableRow column count (Glauber Costa)
- Repair page cache memory leak during balancing (Preston Thorpe)
- Fix `sim-schema` command (Pedro Muniz)
- Propagate decryption error out of the callback (Avinash Sajjanshetty)
- Fix sorter column deduplication (Piotr Rżysko)
- Restore missing functions after revert (Pedro Muniz)
- ci: repair merge-pr issue by escaping command-line backticks (Ceferino Patino)
- Correct multiple integrity_check issues (Jussi Saurio)
- core/io: Repair Android and iOS builds (Pekka Enberg)
- WAL txn: correct reads from DB file (Nikita Sivukhin)
- Correct JavaScript blob type handling (Pekka Enberg)
- Correct all-index updates when rowid changes (Jussi Saurio)
- Correct UPDATE to insert rowid into index rather than NULL (Jussi Saurio)
- Correct DELETE table name normalization (Jussi Saurio)
