# Turso 0.0.21: added

Open when checking these changes in Turso 0.0.21, released 2025-05-28. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.21-index.md)

## Added

- Add a Schema reference to Resolver for adhoc subquery planning (Jussi Saurio)
- Implement user_version pragma with the SetCookie opcode (meteorgan)
- Add libsql_wal_get_frame() API (Pekka Enberg)
- Fix bug: make op_vopen replace the cursor slot instead of adding a new one (Jussi Saurio)
- bind/js: support bind() method and cut down boilerplate (Diego Reis)
- Add PThorpe92 to codeowners file for extensions and go bindings (Preston Thorpe)
- Refactor: add stable internal_id property on TableReference (Jussi Saurio)
- refactor: add walk_expr() and walk_expr_mut() to reduce repeated pattern matching (Jussi Saurio)
- Add comments for values statement handling (meteorgan)
- Repair bindings/wasm wal file creation by implementing `generate_random_number` (오웬)
- core/pragma: support updating user_version (Diego Reis)
- Support values statement plus values in select (meteorgan)
- Add initial Nested Translation support (Pedro Muniz)
- bindings/rust: Add pragma methods (Diego Reis)
- Add collation column on Index struct (Jussi Saurio)
- Add DISTINCT aggregate function support (Jussi Saurio)
- bindings/javascript: Add Statement.iterate() method (Diego Reis)
- (btree): add handling for offset-based payload access with overflow support (Krishna Vishal)
- Add labeler workflow while reorganizing macros (Preston Thorpe)
- Update Nyrkiö change detection to the newest version (Henrik Ingo)
- perf/ci: add basic tpc-h benchmark (Jussi Saurio)
- Add `libsql_wal_frame_count()` API (Pekka Enberg)
- Restructure optimizer so join reordering is supported (Jussi Saurio)
- Add `rustfmt` into rust-toolchain.toml (Pekka Enberg)
