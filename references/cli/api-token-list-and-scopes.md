# API token list and scopes

[Context index](authentication-and-contact-commands.md) · [Continue](api-token-minting-and-permissions.md)

## List

Run this command to show every API token you created for organizations where you are a member:

```
turso auth api-tokens list

```

 The output is a table:

```
NAME            SCOPE                 PERMISSIONS                                CREATED AT
ci-bot          all                   —                                          2026-04-12
deploy-bot      my-org                —                                          2026-05-02
provisioner     my-org/default        read-only                                  2026-05-21
schema-bot      my-org/default        db:create, db:configure, db:mint-token     2026-05-23
admin-bot       my-org/default        full-access                                2026-05-24

```

- **Scope** shows how the token is bound:
  - `all` - no organization restriction; the token can act on every organization associated with the caller.
  - `<org>`: organization-scoped.
  - `<org>/<group>`: group-scoped.

- **Permissions** shows the grant recorded for group-scoped tokens:
  - `—` when no explicit scope is set, which applies to unrestricted and org-scoped tokens.
  - `read-only` means the only granted scope is `read`.
  - `full-access` means every scope is granted.
  - Otherwise, scopes are displayed as a comma-separated list.

 The output reflects the platform's stored representation. Creation presets like `--read-only` and `--full-access` are saved as the underlying individual scopes, so the list shows those expanded scopes.
