# Turso 0.6.0: updated functions and runtime

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Updated

- move `translate_expr` code into standalone files (Pedro Muniz)
- core/vdbe: propagate yield-completions across nested-statement IO yields (Mikaël Francoeur)
- Refactor ` ProgramState` opcode state into one enum plus Box commit state machines (Pedro Muniz)
- core/translate: lower stack usage through boxing (Pere Diaz Bou)
- core/connection: consolidate uses of `is_memory_like` and `is_memory_path` (Avinash Sajjanshetty)
- core/translate: Make `BitSet` struct generic (Mikaël Francoeur)
- core/translate: avoid reallocating TableReferences during extend operations (Pedro Muniz)
- core: small refactors plus slop-removal cleanup (Preston Thorpe)
- swap container types over to BitSet (Mikaël Francoeur)
- core/translate: remove several clones (Pere Diaz Bou)
- swap HashSet out for ColumnMask (Mikaël Francoeur)
- replace TableMask with BitSet (Mikaël Francoeur)
- core: release the `DATABASE_MANAGER` lock after lookup finishes (Pedro Muniz)
- Empty result callbacks pragma (Pekka Enberg)
- parser: Reject "#" as an illegal token (Pekka Enberg)
- core: map the database registry by file ID (Pekka Enberg)
- make OnSerializationComplete Callback return a `Result<()>` (Pedro Muniz)
- core: store MonotonicInstant as u128 (Elina)
- core: adjust null handling in json_string_to_db_type (Patrick Pichler)
- Arrays (Glauber Costa)
- Group of small perf tuneups (Jussi Saurio)
- Optimize step() (Pekka Enberg)
- core/vdbe: pass &Arc rather than Arc to Program::step() (Pekka Enberg)
