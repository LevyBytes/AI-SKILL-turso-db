# Authentication token and user identity

[Context index](authentication-and-contact-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](revoke-tokens-and-manage-login.md) · [Continue](contact-meetings-and-feedback.md)

## Token

Run this command to print the current API token used for Turso Platform API authentication:

```
turso auth token

```

## Whoami

Run this command to display the signed-in user's username:

```
turso auth whoami

```

## login

### `login`

 The Turso CLI authenticates to Turso with an access token. Log in with this command:

```
turso auth login

```

 The command opens a browser and asks you to sign in through GitHub. For Windows with WSL or remote CI/CD, use `--headless`. After login, account and database management commands are available. See the docs for all commands, or inspect a subcommand with `--help`:

```
turso

```

 The Turso CLI asks for authentication again after one week. Keep your token private.

### `signup`

 New Turso users can create an account from the Turso CLI:

```
turso auth signup

```

### `logout`

 Use `logout` to sign out of the Turso CLI:

```
turso auth logout

```
