# Turso 0.5.0: added schema and constraints

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Added

- introduce return type checking for Function calls in `CHECK` constraints (Pedro Muniz)
- autofix: implement FkCheck opcode to block RETURNING rows after FK violations (Jussi Saurio)
- Fix STRICT ADD COLUMN default type validation (Kumar Ujjawal)
- fix: ensure NEW.rowid_alias in UPDATE Trigger WHEN Clause does not evaluate to NULL (Jussi Saurio)
- fix: Return safely instead of panicking for ALTER TABLE ADD COLUMN with invalid collation name #5305 (Jussi Saurio)
- Implement CHECK constraints (Glauber Costa)
- compat.md: add generated + md reformat (Pere Diaz Bou)
- Handle DEFAULT value behavior for NON-NULL columns in ADD COLUMN (Preston Thorpe)
- Provide json_object(*) to create JSON object from all columns (Martin Mauch)
