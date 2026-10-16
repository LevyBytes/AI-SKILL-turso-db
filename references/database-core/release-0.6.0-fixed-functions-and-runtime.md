# Turso 0.6.0: fixed functions and runtime

Open when checking these changes in Turso 0.6.0, released 2026-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.6.0-index.md)

## Fixed

- Move logging output to debug level (Pekka Enberg)
- core: Fix cast numeric (Jean Arhancet)
- core/vdbe: Correct AVG() function precision for large integers (Hossam Khero)
- core/vdbe: Correct string truncation for substr() and like (Rohith Suresh)
- core/translate: Fix concat() function error message (Pekka Enberg)
- core/translate: Fix integrity check panic (Mikaël Francoeur)
- cli: Correct ".tables" output so it includes views like in SQLite (Rupesh Harode)
- fix: make unhex(X, Y) ignore separator characters inside the input (Rupesh Harode)
- parser: align parse error messages with SQLite (Pekka Enberg)
- core/translate: propagate function resolution errors (Pekka Enberg)
- Make MATCH operator on non-FTS tables return an error (Gaurav Sarma)
- core/json: Correct json_array_length(NULL) returning 0 instead of NULL (Artur Kantorczyk)
- core/vdbe: Correct `unicode(char(0))` so it returns NULL (Artur Kantorczyk)
- fix: decode JSON string escape sequences in JSON_EXTRACT and ->> operator (Mikaël Francoeur)
- fix: let RAISE() accept non-literal string expressions for error message (Preston Thorpe)
- Show consistent error message for `i_am_a_dummy` pragma (Avinash Sajjanshetty)
- fix: make json_group_array/json_group_object return [] / {} on empty input (Abhishek)
- fix(json): emit unquoted strings as plain SQL text (ratnesh mishra)
- core/translate: Correct panic when PRAGMA query_only receives float literal (Sergio)
- core/vdbe: Correct SUBSTR() with negative offsets for multi-byte UTF-8 strings (ratnesh mishra)
- core/translate: Return an error instead of panicking for list_types=1 pragma (Aly Abdelmoneim)
