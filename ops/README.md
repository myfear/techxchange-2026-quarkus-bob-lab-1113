# LAB-1113 VM preload inventory

Use these lists to prepare each participant VM for the complete lab, including the optional Qute exercise. Inventory date: **October 1, 2026**. Application version: **Quarkus 3.39.5**. MCP server version in the verified run: **1.0.10**.

| File | Contents |
| --- | --- |
| [container-images.txt](container-images.txt) | Seven image references, including two tags for the documentation image |
| [maven-artifacts.csv](maven-artifacts.csv) | 464 distinct artifact coordinates for application, dev, test, build plugins, platform metadata, and MCP |
| [inventory-pom.xml](inventory-pom.xml) | Unchanged POM from the completed, tested lab application; input for dependency resolution |

These are inventories for ops to preload. They do not install software, create containers, or modify the participant workspace. The POM is a reference input, not a starter project to give participants.

## Container images

| Image | Purpose and evidence |
| --- | --- |
| `docker.io/library/postgres:16` | Explicit database container in the container exercise; verified lab run |
| `docker.io/library/postgres:18` | Dev Services database observed in the verified Quarkus 3.39.5 run |
| `registry.access.redhat.com/ubi9/openjdk-25-runtime:1.24` | JVM application base image in the tested `Dockerfile.jvm` |
| `docker.io/testcontainers/ryuk:0.14.0` | Cleanup helper referenced by Testcontainers 2.0.5 and by the MCP runner; include even if the VM disables Ryuk |
| `docker.io/library/alpine:3.17` | Testcontainers startup-check image; can be needed on a fresh VM |
| `ghcr.io/quarkusio/chappie-ingestion-quarkus:latest` | Local documentation-search container started by Quarkus Agent MCP |
| `ghcr.io/quarkusio/chappie-ingestion-quarkus:3.36.0` | Versioned tag matching the cached `latest` image in the verified run |

The MCP server runs as a JBang Java process. Its semantic documentation search starts the Chappie container. The verified run could not find a documentation index for 3.39.5 and returned results indexed for 3.36.0. Keep the application on 3.39.5. Both documentation tags above referred to the same locally cached image when this inventory was captured; `latest` can change.

Preserve the image names and tags requested by the tools, including `latest`. Select manifests for the lab VM's CPU architecture. The reference run used macOS arm64, so this inventory does not certify Linux amd64 image availability. The lab builds `localhost/greeting-service:lab` itself; it is not an image to preload. No native-image builder is needed.

## Maven artifacts

The CSV columns identify `groupId`, `artifactId`, `type`, `classifier`, `version`, and `used_by`. An empty classifier means the ordinary artifact. Multiple `used_by` values are separated by semicolons. Different versions of the same library can be required by separate plugin and application classpaths; retain them.

The application uses these direct dependencies:

- `io.quarkus:quarkus-rest:3.39.5`
- `io.quarkus:quarkus-arc:3.39.5`
- `io.quarkus:quarkus-rest-jackson:3.39.5`
- `io.quarkus:quarkus-hibernate-orm-panache:3.39.5`
- `io.quarkus:quarkus-jdbc-postgresql:3.39.5`
- `io.quarkus:quarkus-container-image-podman:3.39.5`
- `io.quarkus:quarkus-rest-qute:3.39.5` (optional exercise)
- `io.quarkus:quarkus-junit:3.39.5` (tests)
- `io.rest-assured:rest-assured:6.0.1` (tests)

The CSV also includes transitive runtime and Quarkus deployment dependencies for production, dev mode, and tests. Build-plugin coverage includes the Quarkus plugin 3.39.5, compiler 3.15.0, Surefire/Failsafe 3.5.6, resources 3.5.0, and clean 3.2.0, plus their resolved dependencies and the Surefire JUnit Platform provider. The clean plugin is included for preparation/recovery; participants must not run `mvn clean` while dev mode is running.

Resolve coordinates through Maven so their accompanying POMs, parent POMs, imported BOMs, and repository metadata are retained. The CSV is an artifact-coordinate list, not a list of every cache file. It excludes the application's own `1.0.0-SNAPSHOT` artifact, which participants build.

Quarkus build-time dependencies require Quarkus-aware resolution. Standard Maven dependency goals alone do not capture them. See [Quarkus offline dependency resolution](https://quarkus.io/guides/maven-tooling/#go-offline).

## MCP, JBang, and project creation

Include **`io.quarkus:quarkus-agent-mcp:jar:runner:1.0.10`**, which is present in the CSV. This is the executable runner JAR; its bundled libraries do not need separate Maven downloads just to launch the runner.

The guide invokes `quarkus-agent-mcp@quarkusio`. The cached JBang catalog maps that alias to `io.quarkus:quarkus-agent-mcp:RELEASE:runner`, so it can select a newer release. Preserve the JBang catalog and resolution cache used to prepare the VM as well as the Maven artifact. Resolving the alias afresh is not a guarantee of version 1.0.10. The guide's invocation has not been changed by this inventory.

Project creation and extension discovery also use catalog metadata. The CSV includes the 3.39.5 platform descriptor JSON and platform properties. The reference cache additionally contained these mutable Quarkus registry coordinates:

- `io.quarkus.registry:quarkus-registry-descriptor:json:1.0-SNAPSHOT`
- `io.quarkus.registry:quarkus-platforms:json:1.0-SNAPSHOT`, including classifier `3.39.5`
- `io.quarkus.registry:quarkus-non-platform-extensions:json:1.0-SNAPSHOT`, including classifier `3.39.5`

Those registry snapshots and their Maven metadata are separate from the fixed-version CSV. They come from `https://registry.quarkus.io/maven`; the release artifacts resolve from Maven Central. Snapshot timestamps can change during VM preparation.

## Installed tools

The verified run used JDK 25 (Temurin), Maven 3.9.16, JBang 0.138.0, Quarkus CLI 3.39.1, Podman 6.1.3, and IBM Bob 2.2.1. These are observed versions, not a requirement to replace the lab VM's supported packages. The guide expects Java 25, Maven 3.9.x, JBang, Quarkus CLI, Podman, and a working Bob installation. Keep the application and Maven plugin at 3.39.5 regardless of the CLI version.

## Evidence and limits

The input POM comes from the completed lab run with nine passing tests, including Qute. Its SHA-256 is `acf94bf5dc9a8b184dc51af297f1e249bab5fa8fe708a5e9d3bd74e5002753e2`.

The inventory was generated from that POM with `io.quarkus:quarkus-maven-plugin:3.39.5:dependency-tree` for each of `prod`, `dev`, and `test`. Plugin dependencies were resolved with `org.apache.maven.plugins:maven-dependency-plugin:3.11.0:resolve-plugins`, restricted to the build plugins listed above. Those inventory commands succeeded using the existing Maven cache, and every listed artifact was checked against a local file. Helper image tags were checked in the Testcontainers and MCP runner JARs; database and application image references came from the verified run and Dockerfile.

This pass did not validate a fresh lab VM or block network downloads through the entire create/dev/test/package workflow. Platform-specific artifacts, dynamic plugin downloads, registry snapshots, and updates behind the MCP alias or image tags remain verification points for ops. Bob sign-in and model-service connectivity are separate from artifact preloading. MkDocs and GitHub Actions dependencies are only needed to publish this documentation site and are outside the participant inventory.
