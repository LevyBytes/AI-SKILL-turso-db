# Turso 0.0.11: changes

Open when checking these changes in Turso 0.0.11, released 2024-12-31. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.0.11-index.md)

## Added

- Add in-memory mode for Python bindings (Jean Arhancet)
- Add json_array_length function (Peter Sooley)
- Add UUID extension support (Preston Thorpe)
## Changed

- Turn on sqpoll by default in io_uring (Preston Thorpe)
- Simulator improvements (Alperen Keleş)
## Fixed

- Correct escaping behavior for like and glob functions (Vrishabh)
- Prevent `sqlite_version()` out-of-bound panics' (Diego Reis)
- Correct on-disk file format bugs (Jussi Saurio)
