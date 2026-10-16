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

```
#!/usr/bin/env php
<?php

use Turso\Todo\CLI\Todo;

require_once __DIR__ . '/vendor/autoload.php';

$header = 'TODO CLI + TURSO';

echo $header . PHP_EOL;
function main()
{
    echo str_repeat('-', WIDTH - 18) . "  Todo CLI - " . VERSION . PHP_EOL;

    while (true) {
        echo "[L] List | [A] Add | [E] Edit | [D] Delete | [X] Exit" . PHP_EOL;
        echo "command: ";

        $choice = trim(fgets(STDIN));

        $app = new Todo();

        switch (strtolower($choice)) {
            case 'l':
                $app->listTodos();
                break;
            case 'a':
                $app->addTodo();
                clearScreen();
                break;
            case 'e':
                $app->editTodo();
                clearScreen();
                break;
            case 'd':
                $app->deleteTodo();
                clearScreen();
                break;
            case 'x':
                exit("Goodbye!" . PHP_EOL);
            default:
                echo "Invalid choice." . PHP_EOL;
                clearScreen();
        }
    }

    echo PHP_EOL;
}

main();

```

### Step 6: Your PHP Application Ready!

(Figure: PHP TODO CLI - TURSO)

### Examples

### [PHP Todo CLI + Doctrine DBAL](https://github.com/tursodatabase/example/php-todo-cli-doctrine-dbal/tree/master)

See the full source code

### [Symfony Rest API CRUD](https://github.com/tursodatabase/example/symfony7-rest-api-crud/tree/master)

See the full source code
