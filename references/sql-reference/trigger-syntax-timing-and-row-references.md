# Trigger syntax timing and row references

[Context index](trigger-definition-reference.md) · [Continue](trigger-conditions-raise-and-multiple-statements.md)

### CREATE TRIGGER

 Because this capability is experimental, it must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). Use a trigger to run one or more SQL statements automatically when table rows are inserted, updated, or deleted.

### Syntax

```
CREATE [TEMPORARY] TRIGGER [IF NOT EXISTS] [schema-name.]trigger-name
    [BEFORE | AFTER | INSTEAD OF]
    {INSERT | UPDATE [OF column-name, ...] | DELETE}
    ON table-name
    [FOR EACH ROW]
    [WHEN expression]
BEGIN
    statement;
    [statement; ...]
END;

```

### Description

 A trigger stores SQL statements that are invoked automatically when a chosen data-change event happens on a table or view. Those statements run in the transaction that caused the trigger to fire; rolling that transaction back also undoes the trigger work.

### Parameters

| Parameter | Description |
| --- | --- |
| `TEMPORARY` | Places the trigger in a temporary database. Only the current connection can see it, and it is removed when that connection ends. `TEMP` is also valid. |
| `IF NOT EXISTS` | Suppresses the error that would otherwise occur when another trigger already uses the requested name. |
| `schema-name` | Identifies the attached database where the trigger is stored. If omitted, the main database is used. It cannot be combined with `TEMPORARY`. |
| `trigger-name` | Name assigned to the trigger; it must be unique within that database. |
| `BEFORE` / `AFTER` / `INSTEAD OF` | Controls whether the trigger runs before, after, or instead of the event that activates it. The default is `BEFORE`. |
| `INSERT` / `UPDATE` / `DELETE` | Specifies which data-change operation causes the trigger to run. |
| `OF column-name, ...` | Used only on UPDATE triggers; it limits firing to updates that modify the listed columns. |
| `table-name` | Names the table monitored by the trigger, or the view when defining INSTEAD OF triggers. |
| `FOR EACH ROW` | Runs the trigger one time for each row changed. This row-level behavior is the sole supported mode and remains the default when unspecified. |
| `WHEN expression` | Optional predicate used to decide whether the trigger body should run for a row; execution happens only when the expression is true. |

### Trigger Timing

### BEFORE Triggers

 A `BEFORE` trigger runs prior to the event changing the row. BEFORE triggers are useful when data must be checked or adjusted before storage.
- `NEW` exposes the values that will be stored. In INSERT triggers, `NEW` is the incoming row. In UPDATE triggers, `NEW` holds the post-update values.
- `OLD` can be used in UPDATE and DELETE triggers; it represents the row values as they existed before the change.
- When a BEFORE trigger raises an error, processing for that row stops and the operation that caused it is canceled.

### AFTER Triggers

 An `AFTER` trigger runs once the row has already been changed by the triggering statement. Typical uses include logging, audit trails, and follow-on changes in other tables.
- `NEW` and `OLD` keep the same meanings here that they have in BEFORE triggers.
- By the time the trigger body runs, the row change has already been applied.

### INSTEAD OF Triggers

 An `INSTEAD OF` trigger is valid only for a view. It runs as the replacement for the INSERT, UPDATE, or DELETE that would target the view, which can make the view writable.
- The INSERT, UPDATE, or DELETE against the view itself is not performed. Logic in the trigger body must apply the intended changes to the base tables.

### Row References

 Within a trigger body, the special row references `NEW` and `OLD` let statements read column values.

| Reference | INSERT | UPDATE | DELETE |
| --- | --- | --- | --- |
| `NEW.column` | Value being inserted | Updated value | Not available |
| `OLD.column` | Not available | Value before update | Value being deleted |

```
-- Access individual columns
NEW.email
OLD.status

```
