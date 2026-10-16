# Turso 0.0.16: changes

Open when checking these changes in Turso 0.0.16, released 2025-03-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.16-index.md)

## Added

- Virtual table support (Preston Thorpe)
- Enhance Java bindings (Kim Seon Woo)
- Enhance Rust bindings (Pekka Enberg)
- Add sqlean ipaddr extension (EmNudge)
- Include "dump" and "load" in the help menu (EmNudge)
- Initial Antithesis testing tool (Pekka Enberg)
## Fixed

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
