# Shared session creation and resumption

[Context index](filesystem-and-session-workflows.md) · [Continue](session-supervision-storage-and-management.md)

## Creating a Session

Named sessions allow multiple agents, terminals, or runs to share one filesystem. Everyone attached sees the same copy-on-write project view.

### Creating a Session

 Create a named session with `--session`:

```
agentfs run --session my-project /bin/bash

```

 This creates the `my-project` session, with changes stored in `.agentfs/my-project.db`.

### Resuming a Session

 Run the command again to continue from the previous state:

```
# First run - make some changes
agentfs run --session my-project /bin/bash
$ echo "hello" > test.txt
$ exit

# Second run - changes are still there
agentfs run --session my-project /bin/bash
$ cat test.txt
hello

```

### Multi-Terminal Collaboration

 More than one terminal can connect to the same session at the same time:

```
# Terminal 1
agentfs run --session shared-work /bin/bash

# Terminal 2 - joins the same filesystem
agentfs run --session shared-work python3 agent.py

# Terminal 3 - also sees the same files
agentfs run --session shared-work vim

```

 Every terminal uses the same copy-on-write filesystem, so edits from one terminal appear immediately in the others.

### Use Cases

### Iterative Development

 Continue work on a task through multiple sessions:

```
# Morning session
agentfs run --session feature-x /bin/bash
# ... work on the feature, then exit for lunch

# Afternoon session - continue where you left off
agentfs run --session feature-x /bin/bash

```

### Safe Experimentation

 Start a session, attempt a risky change, and then choose whether to keep it:

```
agentfs run --session experiment /bin/bash
# ... try some changes

# Option A: Happy with changes
agentfs diff experiment  # Review
# Apply changes to real filesystem (manually or via script)

# Option B: Discard everything
rm .agentfs/experiment.db

```
