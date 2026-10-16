# Java local build and dependency

[Context index](client-connection-and-vercel-workflows.md) · [Continue](javascript-local-query-results.md)

## Java

### Step 1: Install

The package has not been published to Maven Central yet. To use it for now, build the JAR locally and publish it into your local Maven repository:

```
cd bindings/java

# Please select the appropriate target platform, currently supports `macos_x86`, `macos_arm64`, `windows` and `linux_x86`
make macos_x86

# deploy to maven local
make publish_local

```

Then declare the dependency this way:

```
dependencies {
    implementation("tech.turso:turso:0.0.1-SNAPSHOT")
}

```
