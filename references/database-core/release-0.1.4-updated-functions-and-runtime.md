# Turso 0.1.4: updated functions and runtime

Open when checking these changes in Turso 0.1.4, released 2025-08-20. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.4-index.md)

## Updated

- unify halts (Glauber Costa)
- Hand-convert the SQLite parser in Rust (Lâm Hoàng Phúc)
- Refactor: atomic ordering (Preston Thorpe)
- IO cleanups for use and (Pedro Muniz)
- More State machines (Pedro Muniz)
- Support single, double, and unquoted strings in values clause (Mikaël Francoeur)
- Feat/pragma module list (Lucas Forato)
- Limit global allocator setup to executables, not library code (Pedro Muniz)
- Restrict arena bitmap values to multiples of 64 for performance (Preston Thorpe)
- Add more IO state machine work (Pedro Muniz)
- Coll seq (Glauber Costa)
- Add more state machine handling and return IO where completions are created (Pedro Muniz)
- Add more State Machines ahead of IO Completions tracking (Pedro Muniz)
- Keep files in the IO memory backend (Nikita Sivukhin)
- Make completions idempotent (Preston Thorpe)
- perf/vdbe: eliminate eager cloning in op_comparison (Jussi Saurio)
- Use a state machine for NoConflict opcode (Mikaël Francoeur)
- state_machine: remove State associated type (Pere Diaz Bou)
- Require doubled single quotes inside a string literal in (Diego Reis)
- remove non-existent opcode (Glauber Costa)
- Update more function signatures so they return Completions (Pedro Muniz)
- Simplify conversion between InsnFunctionStepResult and StepResult (Diego Reis)
- Update function signatures to return IO Completions (Pedro Muniz)
