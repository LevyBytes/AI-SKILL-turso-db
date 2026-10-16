# Turso 0.1.5: updated testing and builds

Open when checking these changes in Turso 0.1.5, released 2025-09-10. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.5-index.md)

## Updated

- update update-script so JS workspace is handled properly (Nikita Sivukhin)
- use mlugg/setup-zig in place of unmaintained action (Kingsword)
- build native package for ARM64 (Nikita Sivukhin)
- Simulate memory-backed I/O (Pedro)
- Simulate memory-backed I/O (Pedro Muniz)
- Simulator Profiles (Pedro Muniz)
- Fail CI run when Turso output differs from SQLite on TPC-H queries (Jussi Saurio)
- Separate SQL generation from Simulator crate (Pedro Muniz)
- Update PERF.md instructions for running TPC-H (Alex Miller)
- Run compat tests serially and switch UnixIO to Mutex::lock() instead of Mutex::try_lock() (Jussi Saurio)
- sim: remove "run_once faults" (Jussi Saurio)
