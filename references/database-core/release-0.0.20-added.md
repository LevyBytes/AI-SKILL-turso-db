# Turso 0.0.20: added

Open when checking these changes in Turso 0.0.20, released 2025-05-14. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.20-index.md)

## Added

- Add support for isnull and notnull expr (meteorgan)
- Add drop index (Anton Harniakou)
- bindings/wasm: add types property for the typescript setting (오병진)
- Add transaction support to Go adapter (Jonathan Ness)
- Initial `ALTER TABLE RENAME` implementation (Levy A.)
- Add Go adapter support for time.Time and bool data types (Jonathan Ness)
- Add tests covering INSERT with a specified column-name list (Anton Harniakou)
- GROUP BY: refactor logic for cases where sorting is unnecessary (Jussi Saurio)
- Add embedded library support in Go adapter (Jonathan Ness)
- Add Go driver parameter binding support for time.Time (Jonathan Ness)
- Display explanation for the NewRowid opcode (Anton Harniakou)
- Add join ordering concept to plan (Jussi Saurio)
- Add static feature to Cargo.toml for extensions written inside core (Pedro Muniz)
- Add Clone implementation for Arc types (Pete Hayman)
- Add PRAGMA schema_version (Anton Harniakou)
- Support literal-value current_time, current_date, and current_timestamp (meteorgan)
- Add op_idx_delete state machine plus DeleteState simplification (Pere Diaz Bou)
- Introduce the .indexes command (Anton Harniakou)
- Optimization: initialize `Rustyline` only in a tty (Pedro Muniz)
- Add Antithesis Tests (eric-dinh-antithesis)
- core/types: remove duplicate serialtype implementation (Jussi Saurio)
- bindings/rust: Add Statement.columns() support (Timo Kösters)
- docs: add Rust to the "Getting Started" section (Timo Kösters)
- Add xBestIndex support in vtab API (Preston Thorpe)
- Feat: add descending index support (Jussi Saurio)
