# Changelog (part 11)

> Database Core reference. Original prose; identifiers preserved verbatim.

- Correct test error output truncation (Pedro Muniz)
- Correct Blobs handling in Unary Negate Operation (Pedro Muniz)
- Resolve `AND` Operation incompatibility (Pedro Muniz)
- Fuzzing fix: disable an incorrect assert (Pedro Muniz)
- Address two index-related issues (Jussi Saurio)
- Fuzzing corrections for several operations (Pedro Muniz)
- simulator: refresh bug base and refactor code paths (Alperen KeleÅŸ)
- Correct cell overwrite when the new size is below the cell size (Pere Diaz Bou)
- Make `EXPLAIN` matching case insensitive (Pedro Muniz)
- core: Correct Linux syscall VFS behavior (Pekka Enberg)
- Index insert fixes (Pere Diaz Bou)
- Lower page count during balancing fixes (Pere Diaz Bou)
- Remainder fixes (jachewz)
- Fix virtual table translation issues (Preston Thorpe)
- Correct overflow placement in write_page() (LÃ¢m HoÃ ng PhÃºc)

### 0.0.18 - 2025-04-02

### Added

- Jsonb support update (Ihor Andrianov)
- Add BTree rebalancing after `delete` (Krishna Vishal)
- Introduce Register struct (Pere Diaz Bou)
- Introduce immutable record (Pere Diaz Bou)
- Introduce libFuzzer (Levy A.)
- WAL frame checksum support (Daniel Boll)
- First JavaScript bindings using napi-rs (Pekka Enberg)
- First implementation pass for `UPDATE` support (Preston Thorpe)
- Add `commit()` plus placeholder insert support to the Python binding (Diego Reis)

### Updated

- Build planning support for Update queries (Preston Thorpe)
- Check page cells after every operation (Pere Diaz Bou)
- Move Cli Repl Commands onto clap (Pedro Muniz)
- Let balance_root handle balancing with interior pages (Pere Diaz Bou)
- Let remainder (%) accept textual arguments (Anton Harniakou)
- JSON code cleanups (Pekka Enberg)
- Improve allocations around ImmutableRecord, OwnedRecord and read_record (Pere Diaz Bou)
- JavaScript binding improvements (Pekka Enberg)
- Kill test environment (Pekka Enberg)
- Drop `SpinLock`'s public unlock method to avoid unsafe aliasing (Krishna Vishal)
- Cover the zero limit case in the query plan emitter (Preston Thorpe)
- Reduce MVCC cursor memory consumption (Ihor Andrianov)
- Treat unary `+` as a noop (Levy A.)
- JSON cache (Ihor Andrianov)
- Update `rusqlite` to 0.34 (Pere Diaz Bou)
- core: Rename FileStorage as DatabaseFile (Pekka Enberg)
- Improve Python bindings (Diego Reis)
- Schema translation cleanups (Pekka Enberg)
- Various JSON improvements (Ihor Andrianov)
- Turn on pretty mode by default in the shell (Pekka Enberg)
- Improve CLI color scheme (Pekka Enberg)
- Implement Copy for selected pager types to avoid explicit clones (Preston Thorpe)
- Add syntax highlighting and hints (Pedro Muniz)
- chore: add gitignore coverage for files matching *.db (Anton Harniakou)
- Restructure the extension library and put VFS behind a feature gate (Preston Thorpe)
- fragment bench functions (Pere Diaz Bou)

### Fixed

- Delete crashing balance code that is no longer needed (Pere Diaz Bou)
- Correct divider-cell propagation while balancing an interior page (Pere Diaz Bou)
- Fuzz test btree fix seeking. (Pere Diaz Bou)
- Fix IdxCmp insn comparisons (Jussi Saurio)
- Resolve most or all floating-point math and display problems. (Ihor Andrianov)
- Make BTreeCell/read_payload allocation-free and add overflow fixes (Pere Diaz Bou)
- Correct overflow handling for negated `compute_shl` results (Krishna Vishal)
- Correct a README.md typo (Tshepang Mbambo)
- Fix platform specific FFI C pointer type casts (Preston Thorpe)
- core: Fix Destroy opcode root page handling (Pekka Enberg)
- Fix `SELECT 0.0 = 0` returning false (lgualtieri75)
- bindings/python: Fix flaky tests (Diego Reis)
- Prevent io_uring WAL write corruption by keeping buffers alive (Daniel Boll)

### 0.0.17 - 2025-03-19

### Added

- `BEGIN DEFERRED` support (Diego Reis)
- Experimental MVCC integration (Pekka Enberg)
- `DROP TABLE` support (Zaid Humayun)
- First implementation pass for the Virtual FileSystem extension module (Preston Thorpe)
- JSONB support (Ihor Andrianov)
- Shell command completion (Pedro Muniz)

### Updated

### Fixed

- Rust bindings fixes and enhancements (yirt grek and å—å®«èŒœ)
- Correct transaction management behavior (Pere Diaz Bou and Diego Reis)
- JSON function fixes (Ihor Andrianov)

### 0.0.16 - 2025-03-05

### Added

- Virtual table support (Preston Thorpe)
- Enhance Java bindings (Kim Seon Woo)
- Enhance Rust bindings (Pekka Enberg)
- Add sqlean ipaddr extension (EmNudge)
- Include "dump" and "load" in the help menu (EmNudge)
- Initial Antithesis testing tool (Pekka Enberg)

### Fixed

- SQLite B-Tree balancing algorithm (Pere Diaz Bou)
- B-Tree improvements and corrections (Pere Diaz Bou and Perston Thorpe)
- Show blobs as blob literals in `.dump` (from Mohamed Hossam)
- Correct count() results when the specified column contains a NULL (lgualtieri75)
- Align text-to-integer casting with SQLite' (Preston Thorpe)
- Raise `SELECT 1` performance to match SQLite (Pekka Enberg)
- Correct offset_sec normalization in extensions/time (meteorgan)
- Parse URI values according to the SQLite specification (Preston Thorpe)
- Ensure the LIKE function honors escape characters (lgualtieri75)
- Correct cast_text_to_number compatibility (Pedro Muniz)
- Adjust the LIKE function to accept every type (Mohamed Hossam)

### 0.0.15 - 2025-02-18

### Added

**Core:**

- First implementation pass for virtual tables (Preston Thorpe)
- Bring MVCC code into the source tree -- still disabled (Pekka Enberg, Piotr Sarna, Avinash Sajjanshetty)
- Implement `json_set` (Marcus Nilsson)
- Add early WITH clause support for common table expressions (Jussi Saurio)
- `BEGIN IMMEDIATE` + `COMMIT` support (Pekka Enberg)
- `BEGIN EXCLUSIVE` support (Pekka Enberg)
- Add Printf Support (Zaid Humayun)
- Add `delete` row support (Krishna Vishal)
- Implement json_quote (Pedro Muniz)
- Add read-side user_version pragma handling through the ReadCookie opcode (Jonathan Webb)
- Json path refine (Ihor Andrianov)
- cli: Basic dump support (Glauber Costa)
- Allow numeric column references in GROUP BY (Jussi Saurio)
- Add legacy_file_format pragma implementation (Glauber Costa)
- Add IdxLE and IdxLT opcodes (Omolola Olamide)

**Java bindings:*

- Improve JDBC support, including prepared statements (Kim Seon Woo)
- Rename package name `tech.turso` (Kim Seon Woo)

**Extensions:**

- Sqlean Crypto extension (Diego Reis)
- Sqlean Time extension (Pedro Muniz)
- Add `regexp_replace()` support (lgualtieri75)

**Simulator:**

- Add NoREC testing property (Alperen Keleş)
- Add `--differential` mode against SQLite (Alperen Keleş)

### Fixed

**Core:**

- Fix 24/48 bit width serial types parsing (Nikita Sivukhin)
- Fix substr (Nikita Sivukhin)
- Fix math binary (Nikita Sivukhin)
- Fix and predicate (Nikita Sivukhin)
- Correct IdxGt, IdxGe, IdxLt, and IdxLe instructions (Jussi Saurio)
- Evaluate constant conditions even when a query has no tables (Jussi Saurio)
- Prevent remainder panics with a zero right-hand-side (Jussi Saurio)
- Correct invalid text columns produced by dump (Kingsley Yung)
- Correct CAST text->numeric handling when the valid prefix has length 1 (Jussi Saurio)
- Improve SQL statement prepare performance (Jussi Saurio)
- Fix VCC write conflict handling (Jussi Saurio)
- Address several B-Tree handling bugs (Nikita Sivukhin)
- Correct case handling and emit behavior (Nikita Sivukhin)
- Fix coalesce (Nikita Sivukhin)
- Fix cast (Nikita Sivukhin)
- Fix string funcs (Nikita Sivukhin)
- Correct floating-point truncation in JSON #877 (lgualtieri75)
- Correct `SELECT` handling for mixed-case alias references (Jussi Saurio)

### 0.0.14 - 2025-02-04

### Added

**Core:**

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

**Extensions:**

- First implementation pass for the vector extension (Pekka Enberg)
- Turn on static linking for 'built-in' extensions (Preston Thorpe)

**Go Bindings:**

- Add early Go database/sql driver support (Preston Thorpe)
- Skip potentially expensive work during prepare' (Glauber Costa)

**Java Bindings:**

- Implement JDBC `ResultSet` (Kim Seon Woo)
- Implement LimboConnection `close()` (Kim Seon Woo)
- Add close() for `LimboStatement` and `LimboResultSet` (Kim Seon Woo)
- Add methods to `JDBC4ResultSet` (Kim Seon Woo)
- Load the native library from Jar (Kim Seon Woo)
- Change logger dependency (Kim Seon Woo)
- Log driver loading error (Pekka Enberg)

**Simulator:**

- Add `--load` and `--watch` flags (Alperen KeleÅŸ)

**Build system and CI:**

- Add NyrkiÃ¶ change point detection to the 'cargo bench' workflow (Henrik Ingo)

### Fixed

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

### 0.0.13 - 2025-01-19

### Added

-

Add early native Limbo extension support (Preston Thorpe)

-

Package npm output for node and web (Elijah Morgan)

-

Add `rowid` keyword support' (Kould)

-

Add operators for shift left, shift right, is and is not (Vrishabh)

-

Add regexp extension (Vrishabh)

-

Add simulator counterexample minimization (Alperen KeleÅŸ)

-

Add early prepared-statement value binding (Levy A.)

### Updated

-

Java binding improvements (Kim Seon Woo)

-

Shrink `liblimbo_sqlite3.a` size' (Pekka Enberg)

### Fixed

-

Prevent panics for invalid aggregate function arguments (Krishna Vishal)

-

Correct null compare operations so they return null (Vrishabh)

-

Execute every statement supplied through the SQL argument in CLI (Vrishabh)

-

Fix MustBeInt opcode semantics (Vrishabh)

-

Fix recursive binary operation logic (Jussi Saurio)

-

Correct SQL comment parsing in the Limbo shell (Diego Reis and Clyde)

### 0.0.12 - 2025-01-14

### Added

**Core:**

-

Improve JSON function support (Kacper Madej, Peter Sooley)

-

Support nested parenthesized conditional expressions (Preston Thorpe)

-

Add changes() and total_changes() function support (Lemon-Peppermint)

-

Create indexes automatically in CREATE TABLE when needed (Jussi Saurio)

-

Add partial datetime() function support (Preston Thorpe)

-

SQL parser performance improvements (Jussi Saurio)

**Shell:**

-

Display polished parse errors in the shell (Samyak Sarnayak)

-

Add shell support for CSV imports (Vrishabh)

-

Allow IO backend selection with the --io={syscall,io-uring} argument (Jorge LÃ³pez Tello)

**Bindings:**

-

First Java bindings version (Kim Seon Woo)

-

First Rust bindings version (Pekka Enberg)

-

Add OPFS support in Wasm bindings (Elijah Morgan)

-

Allow uncorrelated FROM clause subqueries (Jussi Saurio)

-

Add in-memory support to `sqlite3_open()` (Pekka Enberg)

### Fixed

-

Make iterate() lazy for JavaScript bindings (Diego Reis)

-

Align integer overflow output with sqlite3 (Vrishabh)

-

Correct 8-bit serial type encoding (Preston Thorpe)

-

Query plan optimizer bug fixes (Jussi Saurio)

-

B-Tree balancing fixes (Pere Diaz Bou)

-

Correct index seek behavior for `SeekOp::LT``SeekOp::GT` (Kould)

-

Correct arithmetic operations for text values' from Vrishabh

-

Correct quote escaping in SQL literals (Vrishabh)

### 0.0.11 - 2024-12-31

### Added

-

Add in-memory mode for Python bindings (Jean Arhancet)

-

Add json_array_length function (Peter Sooley)

-

Add UUID extension support (Preston Thorpe)

### Changed

-

Turn on sqpoll by default in io_uring (Preston Thorpe)

-

Simulator improvements (Alperen Keleş)

### Fixed

-

Correct escaping behavior for like and glob functions (Vrishabh)

-

Prevent `sqlite_version()` out-of-bound panics' (Diego Reis)

-

Correct on-disk file format bugs (Jussi Saurio)

### 0.0.10 - 2024-12-18

### Added

-

In-memory mode (Preston Thorpe)

-

More CLI improvements (Preston Thorpe)

-

Add replace() function support (Alperen KeleÅŸ)

-

Unary operator improvements (Jean Arhancet)

-

Add unex(x, y) function support (Kacper KoÅ‚odziej)

### Fixed

- Correct primary key handling when rowid exists and PK is not alias (Jussi Saurio)

### 0.0.9 - 2024-12-12

### Added

-

Improve CLI (Preston Thorpe)

-

Add iif() function support (Alex Miller)

-

Add last_insert_rowid() function support (Krishna Vishal)

-

Add JOIN USING and NATURAL JOIN support (Jussi Saurio)

-

Add more scalar function support (Kacper KoÅ‚odziej)

-

Add `HAVING` clause support (Jussi Saurio)

-

Add `get()` and `iterate()` to the JavaScript/Wasm API (Jean Arhancet)

### 0.0.8 - 2024-11-20

### Added

- Add Python package build plus example usage (Pekka Enberg)

### 0.0.7 - 2024-11-20

### Added

- Small JavaScript API improvements (Pekka Enberg)
- `CAST` support (Jussi Saurio)

### Fixed

- Correct issues discovered in-btree code with the DST (Pere Diaz Bou)

### 0.0.6 - 2024-11-18

### Fixed

- Prevent database truncation when `limbo-wasm` opens a file in the wrong mode (Pere Diaz Bou)

### 0.0.5 - 2024-11-18

### Added

-

`CREATE TABLE` support (Pere Diaz Bou)

-

Expose Database.prepare() and Statement.all() in Wasm bindings (Pekka Enberg)

-

WAL improvements (Pere Diaz Bou)

-

Add primary key index scans and single-column secondary index scans (Jussi Saurio)

-

`GROUP BY` support (Jussi Saurio)

-

Overflow page support (Pere Diaz Bou)

-

Enhance Python bindings (Jean Arhancet and Lauri Virtanen)

-

Improve scalar function support (Lauri Virtanen)

### Fixed

-

Fix codegen panic with `COUNT(*)` (Jussi Saurio)

-

Make `LIKE` case insensitive (RJ Barman)

### 0.0.4 - 2024-08-22

-

Query planner rewrite (Jussi Saurio)

-

First implementation pass for Python bindings (Jean Arhancet)

-

Expand scalar function support (Kim Seon Woo and Jean Arhancet)

### Added

- Add partial `json()` function support (Jean Arhancet)

### 0.0.3 - 2024-08-01

### Added

-

First write path implementation. The write path remains non-transactional for now. (Pere Diaz Bou)

-

Additional scalar functions: `unicode()` (Ethan Niser)

-

Speed up point queries that use integer keys (Jussi Saurio)

### Fixed

-

Add `ORDER BY` handling for nullable sort columns and qualified identifiers (Jussi Saurio)

-

Prevent `.schema` command crashes in the CLI ([#212](https://github.com/tursodatabase/limbo/issues/212) (Jussi Saurio)

### 0.0.2 - 2024-07-24

### Added

-

Partial `LEFT JOIN` support.

-

Partial `ORDER BY` support.

-

Partial scalar function support.

### Fixed

- Use a POSIX filesystem advisory lock at database open time so concurrent processes cannot corrupt the file. This lock model is not SQLite's transaction-by-transaction POSIX advisory locking; Limbo locks on open because that path is faster. (Issue #94)

### Changed

- Install under `~/.limbo/` rather than `CARGO_HOME`.

### 0.0.1 - 2024-07-17

### Added

-

Add partial `SELECT` statement handling with `WHERE`, `LIKE`, `LIMIT`, `CROSS JOIN`, and `INNER JOIN`.

-

Aggregate function support.

-

`EXPLAIN` statement support.

-

Partial `PRAGMA` statement support, including `cache_size`.

-

Add asynchronous I/O through Linux io_uring direct I/O and Darwin kqueue.

-

First implementation pass for the command line shell and its commands:
