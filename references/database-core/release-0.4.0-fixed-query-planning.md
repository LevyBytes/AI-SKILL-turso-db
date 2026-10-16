# Turso 0.4.0: fixed query planning

Open when checking these changes in Turso 0.4.0, released 2026-01-05. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.4.0-index.md)

## Fixed

- fix(core/translate): apply affinity conversion to hash join build keys and probe keys (Nuno Gonçalves)
- Fix CTE scope propagation in compound SELECTs (Martin Mauch)
- Fix descending index scans that returned rows for a NULL seek key (Jussi Saurio)
- Fix row loss in external sorter when chunks require async IO (Jussi Saurio)
- Fix two compound select bugs (Jussi Saurio)
- Fix translation logic for the IN operator (Nikita Sivukhin)
- Fix optimizer panic when usable constraint refs is empty (Preston Thorpe)
- optimizer: fix wrong index_col_pos when several constraints reference the same join key (Preston Thorpe)
- Bloom filter fixes (Preston Thorpe)
- Return a parse error for NULLS LAST in ORDER BY (Jussi Saurio)
- Fix seek so correct affinity is applied to the seek expr (Pedro Muniz)
- Fix EXISTS handling on LEFT JOIN null rows (Duy Dang)
- Fix INSERT UNION ALL (Duy Dang)
- Fix LEFT JOIN subqueries reusing stale right-side values (Duy Dang)
- Error when duplicate CTE names are detected (Rohith Suresh)
- Fix self-insert SUM for tables using INTEGER PRIMARY KEY (Duy Dang)
