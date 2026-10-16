# CLI setup and command families

[Context index](installation-organization-and-account-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](install-turso-cli-by-platform.md) · [Continue](organization-billing-and-lifecycle.md)

## Introduction

The Turso CLI lets you manage databases, API tokens, user invites, and the database shell from the command line. It also includes a relax command while you wait for a migration. You can manage Turso account resources such as groups, databases, organizations, and invites programmatically through the [Platform API](https://docs.turso.tech/api-reference).

### Step 1: Install

First, install the Turso CLI:

```
brew install tursodatabase/tap/turso

```
### Step 2: Authenticate

Now signup or login:

```
turso auth signup

```
### Step 3: Operate

These commands are available in the Turso CLI:

| Command | Description |
| --- | --- |
| `auth` | Authenticate and manage API tokens. |
| `contact` | Send feedback, share ideas, and schedule time with the team. |
| `db` | Manage databases, create access tokens, and open the shell. |
| `dev` | Run Turso locally for development. |
| `group` | Set up groups for databases that share a location. |
| `org` | Manage billing and invite members. |
| `plan` | `overages`, `select`, `show`, `upgrade` |
| `quickstart` | Start using Turso in 5 minutes. |
| `relax` | Pause for a bit and relax with Turso. |
| `update` | Upgrade the Turso CLI to the latest version with a single command. |
