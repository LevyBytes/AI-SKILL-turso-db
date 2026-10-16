# Turso 0.0.12: changes

Open when checking these changes in Turso 0.0.12, released 2025-01-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.12-index.md)

## Added

### Core

- Improve JSON function support (Kacper Madej, Peter Sooley)
- Support nested parenthesized conditional expressions (Preston Thorpe)
- Add changes() and total_changes() function support (Lemon-Peppermint)
- Create indexes automatically in CREATE TABLE when needed (Jussi Saurio)
- Add partial datetime() function support (Preston Thorpe)
- SQL parser performance improvements (Jussi Saurio)
### Shell

- Display polished parse errors in the shell (Samyak Sarnayak)
- Add shell support for CSV imports (Vrishabh)
- Allow IO backend selection with the --io={syscall,io-uring} argument (Jorge López Tello)
### Bindings

- First Java bindings version (Kim Seon Woo)
- First Rust bindings version (Pekka Enberg)
- Add OPFS support in Wasm bindings (Elijah Morgan)
- Allow uncorrelated FROM clause subqueries (Jussi Saurio)
- Add in-memory support to `sqlite3_open()` (Pekka Enberg)
## Fixed

- Make iterate() lazy for JavaScript bindings (Diego Reis)
- Align integer overflow output with sqlite3 (Vrishabh)
- Correct 8-bit serial type encoding (Preston Thorpe)
- Query plan optimizer bug fixes (Jussi Saurio)
- B-Tree balancing fixes (Pere Diaz Bou)
- Correct index seek behavior for `SeekOp::LT``SeekOp::GT` (Kould)
- Correct arithmetic operations for text values' from Vrishabh
- Correct quote escaping in SQL literals (Vrishabh)
