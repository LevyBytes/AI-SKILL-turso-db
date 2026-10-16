# Turso 0.4.0: fixed schema and constraints

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Fixed

- Fix DROP TABLE handling for FK actions and orphaned/NULL FK refs (Preston Thorpe)
- fix(core): prevent ALTER COLUMN from leaving tables with only generated columns (Nuno Gonçalves)
- Triggers: repair ALTER TABLE issues (Jussi Saurio)
- Fix: drop the internal DBSP table when a materialized view is dropped (Martin Mauch)
- Fix error handling for provided insert column count mismatch (Jussi Saurio)
