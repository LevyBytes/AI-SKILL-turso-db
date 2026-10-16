# Overlay experiments storage and whiteouts

[Context index](filesystem-and-session-workflows.md) · [Setup and preceding context](overlay-layers-creation-and-diffs.md) · [Continue](overlay-database-schema-and-guides.md)

### Safe Refactoring

 Have an agent refactor code while keeping the source tree protected:

```
cd my-project
agentfs run --session refactor python3 refactor_agent.py

# Review what changed
agentfs diff refactor

# Happy? Apply changes manually or via script
# Not happy? Just delete the session
rm .agentfs/refactor.db

```

### Testing Destructive Operations

 Try something dangerous safely:

```
agentfs run --session test /bin/bash
$ rm -rf src/  # Yikes!
$ exit

# Original files are fine
ls src/  # Still there!

# Only the overlay was affected
agentfs fs ls test  # Shows the deletion

```

### Parallel Experiments

 Try several experiments against the same codebase:

```
# Experiment A
agentfs run --session exp-a python3 approach_a.py

# Experiment B (same base, different changes)
agentfs run --session exp-b python3 approach_b.py

# Compare results
agentfs diff exp-a
agentfs diff exp-b

```

### Storage Efficiency

 The overlay only stores:
- **Changed files** - A full copy is stored after the first write
- **New files** - The full file lives in delta
- **Deleted files** - Small whiteout marker
 Original files are not copied. In a 10GB project, 1MB of edits consumes only ~1MB in the delta layer.

### Technical Details

### File Operations

| Operation | Behavior |
| --- | --- |
| Read existing file | Pass through to base |
| Write existing file | Copy to delta, then write |
| Create new file | Write directly to delta |
| Delete file | Create whiteout in delta |
| Rename file | Delete old + create new |

### Whiteouts

 Deleting a base file causes AgentFS to write a "whiteout", which is a marker that masks the file:

```
-- Whiteouts in the database
SELECT * FROM fs_whiteout;

```
