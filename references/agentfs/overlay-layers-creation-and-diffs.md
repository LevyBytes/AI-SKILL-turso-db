# Overlay layers creation and diffs

[Context index](filesystem-and-session-workflows.md) · [Continue](overlay-experiments-storage-and-whiteouts.md)

## How Overlay Works

AgentFS provides copy-on-write isolation through an overlay filesystem, allowing agents to modify files without altering the original data.

### How Overlay Works

 An overlay filesystem is made from two layers:

```
┌────────────────────────────────────┐
│         Merged View (what you see) │
├────────────────────────────────────┤
│  Delta Layer (AgentFS database)    │  ← Writes go here
├────────────────────────────────────┤
│  Base Layer (original directory)   │  ← Read-only
└────────────────────────────────────┘

```

 **Reading a file:**
1. Check delta layer first
2. If the file is absent there, read it from the base layer
 **Writing a file:**
1. Copy the file into the delta layer (when it originated in the base layer)
2. Write changes to delta layer
3. Base layer is never modified
 **Deleting a file:**
1. Add a "whiteout" marker to the delta layer
2. The merged view treats the file as deleted
3. The original file in the base layer is preserved

### Creating an Overlay Filesystem

### With `agentfs run`

 The most direct approach automatically places an overlay on top of your current directory:

```
cd /path/to/project
agentfs run /bin/bash

```

### With `agentfs init --base`

 Create an overlay explicitly:

```
agentfs init my-overlay --base /path/to/project

```

 Then mount it:

```
agentfs mount my-overlay ./workspace

```

 You can also execute a command inside it:

```
agentfs run --session my-overlay /bin/bash

```

### Viewing Changes

 Display differences from the base:

```
agentfs diff my-overlay

```

### Practical Examples
