# Database role permission examples

[Context index](activerecord-and-authentication-workflows.md) · [Setup and preceding context](database-token-scopes-and-permissions.md)

### Role-Based Access Example

 A JWT template can assign permissions according to user roles:

| Role | Permissions |
| --- | --- |
| Admin | `all:data_read,data_add,data_update,data_delete,schema_add,schema_update,schema_delete` |
| Moderator | `all:data_read,data_update` |
| User | `all:data_read` |

 With JWKS, the JWT token carries these permissions from your auth provider's user metadata, such as role or group membership.
