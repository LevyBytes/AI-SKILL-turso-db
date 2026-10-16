# Turso 0.6.0: added functions and runtime

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Added

- Implement PRAGMA foreign_key_list (Mikaël Francoeur)
- core/connection: add a small helper for detecting whether db is in-memory (Avinash Sajjanshetty)
- docs: add multiprocess access details to sql-reference (Preston Thorpe)
- add the series blob profile under `perf/memory` (Pedro Muniz)
- fix: handle bracket-quoted JSON path keys in json_extract (Srinivas A)
- fix: implement .dbconfig dqs_dml for controlling double-quoted string fallback (Srinivas A)
- core: Add read-only handling for the `locking_mode` pragma (Pekka Enberg)
- core/connection: Improve `ATTACH` so fresh databases initialize correctly (Avinash Sajjanshetty)
- core: Add unistr_quote(X) scalar function (Edward)
- core: Implement PRAGMA full_column_names and short_column_names (Pekka Enberg)
- Add statement origin tracking (Avinash Sajjanshetty)
- add unistr(X) scalar function (Edward)
- refactor: Add typed enum SortComparitorType in place of heap String (Preston Thorpe)
- perf/core/vdbe: add setters to Register so the underlying allocation can be reused as much as possible (Jussi Saurio)
