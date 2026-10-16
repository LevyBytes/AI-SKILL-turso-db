# Turso 0.1.2: fixed

Open when checking these changes in Turso 0.1.2, released 2025-07-15. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.2-index.md)

## Fixed

- test/fuzz: make rowid_seek_fuzz a valid fuzz test (Jussi Saurio)
- b-tree: correct seek behavior on a leaf page when no rows match (Nikita Sivukhin)
- Resolve clippy errors for Rust 1.88.0 (Nils Koch)
- sim: return LimboError::Busy for busy state instead of looping forever (Jussi Saurio)
- btree/balance/validation: fix divider cell insert validation (Jussi Saurio)
- btree/balance/validation: repair rightmost ptr validation use-after-free (Jussi Saurio)
- core/translate: correct the "misuse of aggregate function" error message (Pekka Enberg)
- core/translate: report an error when SELECT requires tables but none exist (Mikaël Francoeur)
- antithesis: Fix transaction management (Pekka Enberg)
- core: Fix resolve_function() error messages (Pekka Enberg)
- VDBE: fix op_insert re-entrancy (Jussi Saurio)
- VDBE: fix op_idx_insert re-entrancy (Jussi Saurio)
- bindings/javascript: make error handling more compatible with `better-sqlite3` (Mikaël Francoeur)
- uv run ruff format && uv run ruff check --fix (Jussi Saurio)
- vdbe: fix compilation (Pere Diaz Bou)
- core/translate: repair aggregate star error handling in prepare_one_sele… (Pekka Enberg)
- Repair infinite loops, rollback issues, and additional bugs revealed by I/O fault injection (Pedro Muniz)
- core/translate: standardize no such table error messages (Pekka Enberg)
- Make `ScalarFunc::Glob` handle NULL plus other value types (Krishna Vishal)
- fix: address the buffer pool thread safety problem (KaguraMilet)
- Correct index updates for INTEGER PRIMARY KEY (rowid alias) (Adrian-Ryan Acala)
- Fix Python test import naming (Pedro Muniz)
- Fix boxed memory leaks (Ihor Andrianov)
- bindings/javascript: clean up formatting and typos (Mikaël Francoeur)
