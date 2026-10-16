# Turso 0.0.19: fixed

Open when checking these changes in Turso 0.0.19, released 2025-04-16. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.19-index.md)

## Fixed

- Correct test error output truncation (Pedro Muniz)
- Correct Blobs handling in Unary Negate Operation (Pedro Muniz)
- Resolve `AND` Operation incompatibility (Pedro Muniz)
- Fuzzing fix: disable an incorrect assert (Pedro Muniz)
- Address two index-related issues (Jussi Saurio)
- Fuzzing corrections for several operations (Pedro Muniz)
- simulator: refresh bug base and refactor code paths (Alperen Keleş)
- Correct cell overwrite when the new size is below the cell size (Pere Diaz Bou)
- Make `EXPLAIN` matching case insensitive (Pedro Muniz)
- core: Correct Linux syscall VFS behavior (Pekka Enberg)
- Index insert fixes (Pere Diaz Bou)
- Lower page count during balancing fixes (Pere Diaz Bou)
- Remainder fixes (jachewz)
- Fix virtual table translation issues (Preston Thorpe)
- Correct overflow placement in write_page() (Lâm Hoàng Phúc)
