# Turso 0.0.14: changes

Open when checking these changes in Turso 0.0.14, released 2025-02-04. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.14-index.md)

## Added

### Core

- Enhance changes() and total_changes() functions, with added tests (Ben Li)
- Add `json_object` function support (Jorge Hermo)
- Implemented json_valid function (Harin)
- Implement Not (Vrishabh)
- Add early wal_checkpoint pragma support (Sonny)
- Add Or and And bytecode implementations (Diego Reis)
- Implement strftime function (Pedro Muniz)
- implement sqlite_source_id function (Glauber Costa)
- json_patch() function implementation (Ihor Andrianov)
- json_remove() function implementation (Ihor Andrianov)
- Add isnull / not null handling for filter expressions (Glauber Costa)
- Add offset support in select queries (Ben Li)
- Return column names from prepared statements (Preston Thorpe)
- Implement Concat opcode (Harin)
- Table info (Glauber Costa)
- Pragma list (Glauber Costa)
- Implement Noop bytecode (Pedro Muniz)
- Implement is and is not constraints in where clauses (Glauber Costa)
- Pagecount (Glauber Costa)
- Allow column aliases in GROUP BY, ORDER BY and HAVING (Jussi Saurio)
- Implement json_pretty (Pedro Muniz)
### Extensions

- First implementation pass for the vector extension (Pekka Enberg)
- Turn on static linking for 'built-in' extensions (Preston Thorpe)
### Go Bindings

- Add early Go database/sql driver support (Preston Thorpe)
- Skip potentially expensive work during prepare' (Glauber Costa)
### Java Bindings

- Implement JDBC `ResultSet` (Kim Seon Woo)
- Implement LimboConnection `close()` (Kim Seon Woo)
- Add close() for `LimboStatement` and `LimboResultSet` (Kim Seon Woo)
- Add methods to `JDBC4ResultSet` (Kim Seon Woo)
- Load the native library from Jar (Kim Seon Woo)
- Change logger dependency (Kim Seon Woo)
- Log driver loading error (Pekka Enberg)
### Simulator

- Add `--load` and `--watch` flags (Alperen Keleş)
### Build system and CI

- Add Nyrkiö change point detection to the 'cargo bench' workflow (Henrik Ingo)
## Fixed

- Prevent `select X'1';` from sending limbo into an infinite loop (Krishna Vishal)
- Fix rowid search codegen (Nikita Sivukhin)
- Fix logical codegen (Nikita Sivukhin)
- Prevent parser panics for duplicate column names in `CREATE TABLE` (Krishna Vishal)
- Avoid panics when double-quoted strings appear as column names. (Krishna Vishal)
- Align `SELECT -9223372036854775808` results with SQLite (Krishna Vishal)
- Prevent limbo panics for `SELECT ABS(-9223372036854775808)`. (Krishna Vishal)
- Remove memory leaks and improve extension type efficiency (Preston Thorpe)
- Prevent extra btree creation for tables with a single column PRIMARY KEY (Krishna Vishal)
- Fix null cmp codegen (Nikita Sivukhin)
- Fix null expr codegen (Nikita Sivukhin)
- Fix rowid generation (Nikita Sivukhin)
- Fix shr instruction (Nikita Sivukhin)
- Fix strftime function compatibility problems (Pedro Muniz)
- Avoid fsync for the WAL during read queries (Jussi Saurio)
