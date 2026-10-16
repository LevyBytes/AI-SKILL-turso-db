# Sync conflicts and local replay

[Context index](database-sync-workflows.md)

## Last Push Wins

This setup relies on Turso Cloud for syncing local Turso databases, so it expects an existing account.

### Last Push Wins

 Turso sync resolves conflicts with a **last push wins** approach. If two clients change the same record and both push, whichever push arrives last sets the remote result.

| Time | Client A | Client B |
| --- | --- | --- |
| T1 | `UPDATE users SET name='Alice' WHERE id=1` |  |
| T2 |  | `UPDATE users SET name='Bob' WHERE id=1` |
| T3 | `push()` |  |
| T4 |  | `push()` |

 **Result**: The final name is `'Bob'`, since Client B performed the later push.

### What Happens During Pull

 If a pull occurs while local edits have not yet been pushed:
1. The local database returns to the most recent synced state
2. Remote changes are applied
3. Those pending local edits are **replayed** afterward
 The rollback followed by replay is atomic; on failure, the database stays at its earlier state.
