# Installation platforms and shell completions

[Context index](filesystem-and-session-workflows.md) · [Continue](build-agentfs-from-source.md)

## Quick Install

 The quickest AgentFS installation path:

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

 This places the `agentfs` binary in `~/.cargo/bin`.

### Platform-Specific Instructions

-  macOS
-  Linux
-  Windows

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

Once installation finishes, restart the terminal or run:

```
source ~/.zshrc  # or ~/.bashrc

```

**Using the installer:**

```
curl -fsSL https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.sh | sh

```

Once installation finishes, restart the terminal or run:

```
source ~/.bashrc  # or ~/.zshrc

```

**Requirements:**
- FUSE needs to be present for filesystem mounting
- On Ubuntu/Debian: `sudo apt install fuse3`
- On Fedora: `sudo dnf install fuse3`
**Using PowerShell:**

```
irm https://github.com/tursodatabase/agentfs/releases/latest/download/agentfs-installer.ps1 | iex

```

You can also get the binary directly from the [releases page](https://github.com/tursodatabase/agentfs/releases).

### Verify Installation

```
agentfs --version

```

### Shell Completions

 Turn on tab completion for your shell:

```
# Auto-detect shell
agentfs completions install

# Or specify shell explicitly
agentfs completions install bash
agentfs completions install zsh
agentfs completions install fish

```

 Restart the shell after the completions are installed.
