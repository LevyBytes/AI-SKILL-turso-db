# Revoke tokens and manage login

[Context index](authentication-and-contact-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](api-token-minting-and-permissions.md) · [Continue](authentication-token-and-user-identity.md)

## Revoke

Run this command to revoke an API token in the current organization:

```
turso auth api-tokens revoke <api-token-name>

```

## `turso auth login`

Use this command to authenticate your Turso CLI account:

```
turso auth login

```

 When using Windows with WSL or running from remote CI/CD, pass the --headless flag.

### Flags

| Flag | Description |
| --- | --- |
| `--headless` | Use this when the CLI cannot open or control the system web browser. |

## `turso auth logout`

Run this command to sign out of the Turso CLI:

```
turso auth logout

```

### Flags

| Flag | Description |
| --- | --- |
| `-a, --all` | Revoke all sessions for the current user. |

## `turso auth signup`

Use this command to create a Turso account from the CLI:

```
turso auth signup

```

 For Windows with WSL, or for remote CI/CD runs, pass the --headless flag.

### Flags

| Flag | Description |
| --- | --- |
| `--headless` | Useful when the CLI cannot directly use the system web browser. |
