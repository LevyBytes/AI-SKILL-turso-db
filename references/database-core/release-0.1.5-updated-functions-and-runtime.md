# Turso 0.1.5: updated functions and runtime

Open when checking these changes in Turso 0.1.5, released 2025-09-10. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.5-index.md)

## Updated

- Handle op_column case with null flag set (Jussi Saurio)
- remove &1 (Lâm Hoàng Phúc)
- Remove RefCell usage from Cursor (Pedro Muniz)
- core: handle read_varint edge cases (Sonny)
- mark completion done only after callback execution (Nikita Sivukhin)
- Make windows iterator return no values for shorter slice (Lâm Hoàng Phúc)
- replace selected matches with `match_ignore_ascii_case` macro (Lâm Hoàng Phúc)
- refactor parser fmt (Lâm Hoàng Phúc)
- Handle strings sometimes serving as identifier quoting (Lâm Hoàng Phúc)
- Allow CURRENT_TIMESTAMP to fallback TK_ID (Lâm Hoàng Phúc)
- remove `turso_sqlite3_parser` from `turso_parser` (Lâm Hoàng Phúc)
- Parse booleans as integer literals inside expressions (Preston Thorpe)
- core/translate: remove needless table name clone in parse_table (Pere Diaz Bou)
- core/translate: remove unneessary agg clones (Pere Diaz Bou)
- core/vdbe: Micro-optimize the "zero_or_null" opcode (Pekka Enberg)
- translate: allocate insns with with_capacity (Pere Diaz Bou)
- perf: stop helper methods from constructing PageType (Jussi Saurio)
- refactor/perf: remove BTreePageInner (Jussi Saurio)
- core/util: emit literal and cow rather than replace (Pere Diaz Bou)
- core/translate: sanize_string fast path improvement (Pere Diaz Bou)
- Remove Parser double indirection (Pedro Muniz)
- Remove Parser double indirection (Pedro Muniz)
- Remove Result from the signature (Mikaël Francoeur)
- Eliminate duplicated attribute in (bit-aloo)
- reduce Token cloning in parser (Lâm Hoàng Phúc)
- Replace several refcells for types that trivially impl Copy (Preston Thorpe)
- move check code into the parser (Lâm Hoàng Phúc)
- execute pragmas properly because some may require IO (Nikita Sivukhin)
