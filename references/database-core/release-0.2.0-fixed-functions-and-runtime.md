# Turso 0.2.0: fixed functions and runtime

Open when checking these changes in Turso 0.2.0, released 2025-10-03. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.2.0-index.md)

## Fixed

- Fix COLLATE (Jussi Saurio)
- core/translate: fix rowid affinity (Preston Thorpe)
- Improve cyclic view error handling (Duy Dang)
- Substr fix UTF-8 (Pedro Muniz)
- Anonymous params fix (Nikita Sivukhin)
- Make sum detect numbers inside strings or at string prefixes (Pavan Nambi)
- remove UnterminatedBlockComment error (Lâm Hoàng Phúc)
- Correct complex-expression handling in materialized views (Glauber Costa)
- quoting fix (Nikita Sivukhin)
- fix avg aggregation (Nikita Sivukhin)
- Correct math function compatibility problems (Levy A.)
- Correct is_nonnull returning true for 1 / 0 (Lâm Hoàng Phúc)
- Correct incompatible math functions (Levy A.)
- core: Correct reprepare so statement cursors and registers reset properly (Pedro Muniz)
- Correct function parameter value conversion (Levy A.)
- core: Fix integer/float comparison (Pavan Nambi)
