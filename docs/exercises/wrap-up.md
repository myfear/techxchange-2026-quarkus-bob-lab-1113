# Close the loop

**Time:** 6 minutes

## Recall

Answer this before you expand the note:

*Which of these must go through Quarkus MCP in this lab, and which is a reasonable Maven command after `quarkus_stop`?*

- Creating the project
- Starting dev mode
- Running unit tests while the app is in dev mode
- Building the container image

??? note "Answer"
    Create, start, and tests go through MCP (`quarkus_create`, `quarkus_start`, `quarkus_callTool` / Dev MCP test tools). Packaging the image with `mvn package -Dquarkus.container-image.build=true` (or the equivalent Bob runs after stop) is the Maven step this lab allows. Never run `mvn clean` while dev mode is still running.

Second check: *Why did `/api/greeting` not need Postgres, and why did the containerized `/api/greetings` fail if you only started the app image?*

??? note "Answer"
    The query-parameter endpoint computed a string in memory. The collection endpoint reads and writes a Panache entity. Prod does not start Dev Services, so the app container needs a reachable PostgreSQL on the same Podman network and `%prod` JDBC settings that use that hostname.

## Artifacts

| Artifact | Location |
|----------|----------|
| MCP configuration | `.bob/mcp.json` |
| Bob instructions | `AGENTS.md` (workspace root) |
| Quarkus application | `greeting-service/` |
| Persisted greetings API | `POST` / `GET` `/api/greetings` |
| Container image | `localhost/greeting-service:lab` |
| Optional UI plan | `greeting-service/docs/ui-plan.md` |

## What changed

Bob is the SDLC partner in the editor. Quarkus Agent MCP is the expert toolchain for lifecycle, skills, docs, and tests. You remain the verifier: `curl`, `podman ps`, and tests you actually see pass.

The production-shaped loop is the same one the abstract promised: create REST, persist data, run the app in a container. Dev Services keep development moving. Explicit `%prod` config and a second container are what make the image honest.

## Further reading

Continue with [Further Readings](../further-readings.md) for articles on Bob context, project memory, lifecycle hooks, and Quarkus skills, testing, and packaging.

- [Quarkus Agent MCP](https://github.com/quarkusio/quarkus-agent-mcp) — tool list, IBM Bob `mcp.json` notes, PATH troubleshooting
- [Container images (Quarkus)](https://quarkus.io/guides/container-image) — Podman builder and image coordinates
- [Hibernate ORM with Panache](https://quarkus.io/guides/hibernate-orm-panache)
- [Dev Services](https://quarkus.io/guides/dev-services)
- [Containerizing Quarkus applications and deploying them with Kubernetes](https://developer.ibm.com/tutorials/quarkus-basics-06/) — next step after this lab if you want Kubernetes manifests

## Need help?

See [Troubleshooting](../troubleshooting.md) for sign-in, SDKMAN/Maven, MCP, Dev Services, ports, Podman, and recovery prompts.
