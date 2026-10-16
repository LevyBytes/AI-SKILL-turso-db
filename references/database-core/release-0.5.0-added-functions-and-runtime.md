# Turso 0.5.0: added functions and runtime

Open when checking these changes in Turso 0.5.0, released 2026-03-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.5.0-index.md)

## Added

- perf: add compiler branching hints (Preston Thorpe)
- introduce stable PRAGMA capture_data_changes_conn name while retaining the old one for compatibility (Nikita Sivukhin)
- Finish implementing RAISE (Preston Thorpe)
- Support custom types (Glauber Costa)
- Introduce RAISE() function support (Glauber Costa)
- core: reimplement PRAGMA integrity_check as VDBE and include sqlite parity corpus (Jussi Saurio)
- translate: support row-valued comparison expressions (Jussi Saurio)
- Provide PRAGMA function_list with enum-derived function probing (Glauber Costa)
- Add REGEXP operator support (Glauber Costa)
- Convert panic/todo/unimplemented into proper errors (Mikaël Francoeur)
- core/vdbe: correct newrowid Start state change on IO (Pere Diaz Bou)
- Introduce support for PRAGMA synchronous=NORMAL (Jussi Saurio)
- mac: introduce PRAGMA fullfsync (Jussi Saurio)
- feat(cli): introduce .read command to execute SQL from a file (i cook code)
- Implement Full Text Search feature (Preston Thorpe)
- fix: implement IS TRUE/IS FALSE using correct SQLite semantics (Jussi Saurio)
- Expand PRAGMA integrity_check and introduce PRAGMA quick_check (Jussi Saurio)
- provide .dbtotxt and dbpage vtab (Pavan Nambi)
