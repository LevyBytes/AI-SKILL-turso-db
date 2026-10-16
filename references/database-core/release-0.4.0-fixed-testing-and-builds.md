# Turso 0.4.0: fixed testing and builds

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Fixed

- stress: continue after I/O errors rather than panicking (Pekka Enberg)
- fix: resolve release-build lint warnings for unused variable/import (Khashayar Fereidani)
- Turn on debug_assertions for the antithesis profile (Pekka Enberg)
- fix dockerfiles (Jussi Saurio)
- slightly adjust fixed unstable test (Nikita Sivukhin)
- CI test setup fixes + fix GroupCompletion bug (Pedro Muniz)
- sim: repair apply_snapshot for create table, create index, and drop column (Jussi Saurio)
- aws/sim: apply fixes and tweaks (Jussi Saurio)
- fix/sim: record and apply every alter table op in order (Jussi Saurio)
- fix/sim: handle row modifications in ALTER TABLE correctly (Jussi Saurio)
- antithesis: fix unique constraint exception handling for stress-composer tests (Pekka Enberg)
- Fix Github go workflow (Nikita Sivukhin)
- sim: fix an apply_snapshot bug (Jussi Saurio)
- Sim transaction fixes (Jussi Saurio)
- sim/aws: comment on existing issues rather than skipping duplicates (Jussi Saurio)
- sim: stop ignoring sql execution errors (Jussi Saurio)
- Update nix build and fix it (Alexander Hirner)
