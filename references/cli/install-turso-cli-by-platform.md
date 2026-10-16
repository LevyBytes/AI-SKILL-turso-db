# Install Turso CLI by platform

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](headless-authentication-and-command-help.md) · [Continue](cli-setup-and-command-families.md)

## macOS

 On macOS, the recommended way to install the Turso CLI is Homebrew:

```
brew install tursodatabase/tap/turso

```

 If Homebrew is not your choice, run this command to execute the CLI installer shell script:

```
curl -sSfL https://get.tur.so/install.sh | bash

```

### Linux

 Execute the CLI installer shell script on Linux with this command:

```
curl -sSfL https://get.tur.so/install.sh | bash

```

### Windows

 Windows installation of the Turso CLI requires [WSL](https://learn.microsoft.com/en-us/windows/wsl/install). After WSL is installed, run this in PowerShell:

```
wsl

```

 After that, run the script to install the Turso CLI:

```
curl -sSfL https://get.tur.so/install.sh | bash

```

### Verify installation

 Open a fresh shell, then verify the installation with this command:

```
turso

```
