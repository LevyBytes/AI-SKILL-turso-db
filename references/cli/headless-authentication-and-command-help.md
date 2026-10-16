# Headless authentication and command help

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](group-transfer-unarchive-and-updates.md) · [Continue](install-turso-cli-by-platform.md)

## Headless Mode

When commands need interaction, the Turso CLI may open a browser or pause for more instructions. Pass the `--headless` flag on operations to avoid that behavior:

```
turso auth login --headless

```

 Use the `--headless` flag when running on Windows with WSL or in a remote CI/CD environment.

## Help

Run this command whenever you need a summary of available commands:

```
turso help

```

 To inspect a command's required and optional arguments, pass the `--help` flag to that command:

```
turso auth --help
turso db create --help

```

 For help integrating Turso with your project, [we can help](https://docs.turso.tech/support).
