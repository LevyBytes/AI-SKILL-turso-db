# PHP doctrine driver installation

[Context index](kotlin-and-php-sdk-workflows.md) · [Continue](php-doctrine-credentials-and-connection.md)

## Prerequisites

(Figure: Doctrine DBAL)

### Prerequisites

 Before beginning, confirm that you:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation) before continuing
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup) first
- Use a PHP Application that includes Doctrine DBAL
 Get the newest extension/driver binary build from the [Release](https://github.com/tursodatabase/turso-client-php/releases) page. It is available for:
- Linux
- Mac/Darwin
- Windows (WSL) / [Devcontainer](https://github.com/darkterminal/turso-docker-php)

Turso PHP Installer is limited to Linux/macOS only

Get the installer:

```
curl -o turso-php-installer.phar --proto '=https' --tlsv1.2 -sSf "https://darkterminal.github.io/turso-php-installer/dist/turso-php-installer.phar"
php turso-php-installer.phar

```

Register it as a global executable script:

```
chmod +x turso-php-installer.phar
sudo mv turso-php-installer.phar /usr/local/bin/turso-php-installer

```

**Usage**Install Turso Client PHP / libSQL Extension:

```
turso-php-installer install

```

Check on your console/terminal

```
php -m | grep libsql
liblibsql_php

```

Update Turso Client PHP / libSQL Extension:

```
turso-php-installer update

```

Uninstall Turso Client PHP / libSQL Extension:

```
turso-php-installer uninstall

```

Manual Configuration

- 📦 Extract the archive
- 🗃 Locate somewhere in your machine
- Copy the relative path that points to the extension/driver
- Open `php.ini`, search for `;extension`, and if you are using nano press ctrl+w before searching
- Add `extension=liblibsql_php.so` on the following line in Linux, without a leading `;`
Check on your console/terminal

```
php -m | grep libsql
liblibsql_php

```

 After this, the `LibSQL` class is available in the PHP environment and can be used throughout the PHP project.1
