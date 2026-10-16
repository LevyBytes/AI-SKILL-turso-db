# Turso 0.3.0: updated query planning

Open when checking these changes in Turso 0.3.0, released 2025-10-30. These are historical release records, not current API availability guarantees.

[Release history](release-history.md) · [This release](release-0.3.0-index.md)

## Updated

- translate: Reject correlated subqueries inside HAVING and ORDER BY (Jussi Saurio)
- index_method: Finish integration with query planner (Nikita Sivukhin)
- index method syntax extension (Nikita Sivukhin)
- DBSP aggregations now always return Floats for sum and avg (Glauber Costa)
- Use heap sort for ORDER BY (Nikita Sivukhin)
- Give explicit column aliases binding precedence in orderby (Pavan Nambi)
- translate/select: prevent multiple identical non-aliased table references (Preston Thorpe)
- cli: Make .tables and .indexes include data from attached tables aswell (Konstantinos Artopoulos)
- Permit indexes while iterating rows for UPDATE statements (Jussi Saurio)
- Make INSERT translation modular using emitter context (Preston Thorpe)
- Route aliases to the places they should be used (Pavan Nambi)
- Optimize sorter (Jussi Saurio)
- optimizer: Improve range scans by using upper and lower bounds more efficiently (Jussi Saurio)
- translate: Have bind_and_rewrite_expr() reject unbound identifiers when no referenced tables exist (Jussi Saurio)
- Enforce uniqueness during create unique index (Jussi Saurio)
