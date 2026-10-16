# Turso 0.0.15: changes

Open when checking these changes in Turso 0.0.15, released 2025-02-18. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.15-index.md)

## Added

### Core

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
### Java bindings

- Improve JDBC support, including prepared statements (Kim Seon Woo)
- Rename package name `tech.turso` (Kim Seon Woo)
### Extensions

- Sqlean Crypto extension (Diego Reis)
- Sqlean Time extension (Pedro Muniz)
- Add `regexp_replace()` support (lgualtieri75)
### Simulator

- Add NoREC testing property (Alperen Keleş)
- Add `--differential` mode against SQLite (Alperen Keleş)
## Fixed

### Core

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
