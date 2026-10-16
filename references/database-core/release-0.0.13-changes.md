# Turso 0.0.13: changes

Open when checking these changes in Turso 0.0.13, released 2025-01-19. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.13-index.md)

## Added

- Add early native Limbo extension support (Preston Thorpe)
- Package npm output for node and web (Elijah Morgan)
- Add `rowid` keyword support' (Kould)
- Add operators for shift left, shift right, is and is not (Vrishabh)
- Add regexp extension (Vrishabh)
- Add simulator counterexample minimization (Alperen Keleş)
- Add early prepared-statement value binding (Levy A.)
## Updated

- Java binding improvements (Kim Seon Woo)
- Shrink `liblimbo_sqlite3.a` size' (Pekka Enberg)
## Fixed

- Prevent panics for invalid aggregate function arguments (Krishna Vishal)
- Correct null compare operations so they return null (Vrishabh)
- Execute every statement supplied through the SQL argument in CLI (Vrishabh)
- Fix MustBeInt opcode semantics (Vrishabh)
- Fix recursive binary operation logic (Jussi Saurio)
- Correct SQL comment parsing in the Limbo shell (Diego Reis and Clyde)
