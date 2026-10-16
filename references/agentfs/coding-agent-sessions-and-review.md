# Coding agent sessions and review

[Context index](filesystem-and-session-workflows.md)

## Supported Agents

AgentFS runs AI coding agents such as Claude Code, Codex, and OpenCode in a sandbox where file edits stay separate from the original source tree.

### Supported Agents

| Agent | Command |
| --- | --- |
| Claude Code | `agentfs run claude` |
| Codex | `agentfs run codex` |
| OpenCode | `agentfs run opencode` |
| Any CLI tool | `agentfs run <command>` |

### Workflow

 A typical agentic coding flow looks like this. The example uses Claude Code, and the same pattern applies to OpenCode, Codex, and other agents.

### Step 1: Clone the Repository

 Clone the git repository into a host directory and keep that source tree free of build artifacts. After main is updated, pull into this repository so it stays current.

```
git clone https://github.com/tursodatabase/agentfs.git
cd agentfs

```

### Step 2: Start an AgentFS Session

 Start a session with `agentfs run` and the `--session` flag. Choose a name that can later be reused as your git branch name.
-  Claude Code
-  Codex
-  OpenCode

```
agentfs run --session fix-bug-123 claude

```

```
agentfs run --session fix-bug-123 codex

```

```
agentfs run --session fix-bug-123 opencode

```

 The agent is now working inside a copy-on-write environment, leaving the original source tree unchanged.

### Step 3 (Optional): Manual Investigation

 When the agent behaves unexpectedly, use a second terminal to attach to the same session for inspection:

```
agentfs run --session fix-bug-123 bash

```

 The shell and the agent see the same filesystem. You can browse files, run tests, and observe the current state while the agent continues working.

### Step 4: Push to Branch

 After the agent finishes, tell it to create a branch, commit the code, and push:

>  “Create a branch called fix-bug-123, commit these changes, and push to origin”

 From there, open a PR manually from the pushed branch.

### Why This Workflow?

- **Clean source tree** - The original clone remains clean and simple to update from upstream
- **Separated changes** - Every task has its own session, avoiding change leakage between tasks
- **Simple inspection** - Attach to any session to view activity in real time
- **Standard git flow** - Push branches and open PRs using the normal process

### Next Steps

### Multi-Agent Sessions

Use shared state across agents and terminals

### Auditing

Review agent activity with diff and timeline
