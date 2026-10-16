# Flutter upstream example setup

[Context index](c-and-flutter-sdk-workflows.md)

Open the [libsql_dart example](https://github.com/dikatok/libsql_dart/tree/main/example) when you need the package's complete Flutter application and platform projects.

### Project Contents and Launch

The example contains platform folders for Android, iOS, Linux, macOS, web and Windows, with Dart application code under `lib/`. Configuration includes `.env`, `.gitignore`, `.metadata`, `analysis_options.yaml`, `devtools_options.yaml`, `docker-compose.yaml`, `pubspec.yaml` and `pubspec.lock`; consult its `README.md` for package-specific requirements.

Choose one database source:

- Create a remote Turso database.
- Start a local server using [Turso local development](https://docs.turso.tech/local-development#turso-cli).
- Start the local libSQL instance defined in the example's `docker-compose.yaml`.

Set the example's database credentials in `.env`, then run:

```sh
flutter run --debug --dart-define-from-file=.env
```

The example embeds its configured connection values in the application. Use development credentials; a distributable application needs the platform's intended authentication and token provisioning design.
