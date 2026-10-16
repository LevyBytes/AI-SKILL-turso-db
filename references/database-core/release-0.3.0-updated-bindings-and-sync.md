# Turso 0.3.0: updated bindings and sync

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Updated

- bindings/rust: Set mimalloc as the global allocator (Pekka Enberg)
- Make sqlite3_column_text return null terminated strings (Preston Thorpe)
- bindings/rust: Carry a dropped tx DropBehavior into the next DB access instead of panicing (Jussi Saurio)
- Make Rust bindings actually async (Pedro Muniz)
- bindings/rust: Raise the recommended version to 0.2 (Kyle Kelley)
