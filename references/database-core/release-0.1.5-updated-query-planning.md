# Turso 0.1.5: updated query planning

Open when checking these changes in Turso 0.1.5, released 2025-09-10. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.1.5-index.md)

## Updated

- optimizer: change outer join to inner join where possible (Jussi Saurio)
- Re-evaluate left join seek key condition after null row (Jussi Saurio)
- Add persistence for DBSP-based materialized views (Glauber Costa)
- Centralize aggregate function resolution (Piotr Rżysko)
- Unify grouped and ungrouped aggregation handling (Piotr Rżysko)
- Move views to DBSP circuits (Glauber Costa)
- translate/insert: Improve string format performance (Pere Diaz Bou)
- DBSP projection (Pekka Enberg)
- Use a more structured translate_insert approach (Jussi Saurio)
- refactor/vdbe: move insert-related seeking from BTreeCursor into VDBE (Jussi Saurio)
