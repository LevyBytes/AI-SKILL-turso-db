# Turso 0.4.0: fixed functions and runtime

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Fixed

- Improve core/functions with general cleanup, micro-optimizations, and a bug fix (Khashayar Fereidani)
- Affinity fixes (Pedro Muniz)
- Fix stack overflow for long unary expressions involving ("' (Jussi Saurio)
- Fix RTRIM ignoring trailing tabs (Krishna Vishal)
- Fix TEXT to INTEGER conversion when numeric text has a trailing non-breaking space (Krishna Vishal)
- Fix stack overflow for long unary expressions involving ()
- fix succeeded check (Pedro Muniz)
- Fix coroutine panic by replacing ended_coroutine Bitfield with vec (Jussi Saurio)
- fix(json): properly serialize infinite values (Nuno Gonçalves)
- fix(core/util): reject integer primary key underflow (Nuno Gonçalves)
- core: Fix integrity_check pragma code generation (Pekka Enberg)
- fix: escape backslashes within json_object string values (Martin Mauch)
- Fix vtab memory leak (Nikita Sivukhin)
- Correct comparison behavior for large numbers (Mikaël Francoeur)
- core/io: Improve error handling (Pekka Enberg)
- core/index_method: improve error handling in toy_vector_spare_ivf.rs (Pekka Enberg)
- core/vdbe: fix the incorrect `unreachable` condition in op_seek_rowid (Preston Thorpe)
