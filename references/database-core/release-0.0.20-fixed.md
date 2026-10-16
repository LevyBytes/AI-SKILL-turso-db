# Turso 0.0.20: fixed

Open when checking these changes in Turso 0.0.20, released 2025-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.20-index.md)

## Fixed

- testing/py: rename debug_print() as run_debug() (Jussi Saurio)
- Correct empty string handling in prepared statements (Diego Reis)
- CREATE VIRTUAL TABLE fixes (Piotr Rżysko)
- Bindings/Go: repair symbols for FFI calls (Preston Thorpe)
- Correct parameter binding for insert statements whose column indexes are out of order (Preston Thorpe)
- Repair memory leak from unclosed virtual table cursors (Piotr Rżysko)
- Prevent async io panic caused by reading locked page (Preston Thorpe)
- Fix bug: order by terms cannot be removed from the list head (Jussi Saurio)
- Correct default value setting for primary key on UPDATE (Pere Diaz Bou)
- Fix: allow page_size=65536 (meteorgan)
- Fix `page_count` pragma (meteorgan)
- Repair broken fuzz target caused by old name (Levy A.)
- Emit `IdxDelete` instruction and fix seek after deletion issues (Pere Diaz Bou)
- Bugfix: make Explain command show syntax errors in CLI (Anton Harniakou)
- Correct between expression documentation (Pedro Muniz)
- Fix bug: clear the left join null flag properly (Jussi Saurio)
- Fix out of bounds access in `parse_numeric_str` (Levy A.)
- Fix post balance validation (Pere Diaz Bou)
