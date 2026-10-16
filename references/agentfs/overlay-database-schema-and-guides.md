# Overlay database schema and guides

[Context index](filesystem-and-session-workflows.md) · [Setup and preceding context](overlay-layers-creation-and-diffs.md) · [Previous phase](overlay-experiments-storage-and-whiteouts.md)

### Database Schema

The SQLite delta layer separates node metadata, directory names, file bytes and deletion markers.

| Table | Stored data |
| --- | --- |
| `fs_inode` | Inode number, POSIX mode, size, ownership, link count and timestamps |
| `fs_dentry` | Basename and parent inode mapped to a target inode |
| `fs_data` | Byte chunks keyed by inode and zero-based chunk index |
| `fs_config` | Filesystem settings, including the data chunk size |
| `fs_symlink` | Symbolic-link target for each link inode |
| `fs_whiteout` | Deleted absolute path, parent path and creation timestamp |
| `fs_origin` | Delta inode mapped to its base-layer inode |

Root inode 1 has no parent directory entry. Multiple directory entries may reference the same inode for hard links. A path must be resolved through `fs_dentry`; it is not an `fs_inode` column.

Whiteouts hide base-layer paths during overlay lookup. File data uses `fs_data`, with byte positions determined by the configured chunk size.

Check the [AgentFS Specification](https://github.com/tursodatabase/agentfs/blob/main/SPEC.md) for the complete versioned DDL and lookup rules.

### Related Workflows

- Open when sharing an overlay with another terminal or agent: [sessions](shared-session-creation-and-resumption.md).
- Open when persisting an overlay remotely: [cloud sync](cloud-sync-setup-and-commands.md).
