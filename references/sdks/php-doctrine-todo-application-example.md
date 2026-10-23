# PHP doctrine todo application example

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](php-doctrine-driver-installation.md) · [Previous phase](php-doctrine-credentials-and-connection.md)

Project Structure Example

```
src/
├── helpers.php
└── Todo.php
vendor/
composer.json
composer.lock
todo            # todo executable

```

This is the entrypoint excerpt from a PHP todo CLI using Doctrine DBAL. It requires the project's Composer autoload configuration, `Turso\Todo\CLI\Todo`, and `src/helpers.php` definitions for `WIDTH`, `VERSION` and `clearScreen()`. Use the linked complete project for those dependencies; this excerpt is not a standalone CLI.

The complete program is in the [upstream source](https://raw.githubusercontent.com/tursodatabase/turso-docs/fb78fbb00393f0a9eeab1f302a2bfa48c1725384/sdk/php/orm/doctrine-dbal.mdx).

### Step 6: Your PHP Application Ready!

(Figure: PHP TODO CLI - TURSO)

### Examples

### [PHP Todo CLI + Doctrine DBAL](https://github.com/tursodatabase/example/php-todo-cli-doctrine-dbal/tree/master)

See the full source code

### [Symfony Rest API CRUD](https://github.com/tursodatabase/example/symfony7-rest-api-crud/tree/master)

See the full source code
