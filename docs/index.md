---
hide:
  - toc
---

<p class="lab-kicker">IBM TechXchange 2026 · LAB-1113 · 90 minutes</p>

# Modern Java Development Workflow: Quarkus, Containers, and IBM Bob

<p class="lab-lead">Build a Quarkus REST service with IBM Bob, store greetings in PostgreSQL, and run the application in a Podman container.</p>

Use the Quarkus Agent MCP for project creation, extension guidance, development, and tests. Verify the result yourself with HTTP requests and a running container. If you finish early, add a Qute UI using Bob's Plan and Agent modes.

<div class="lab-actions" markdown>
[Start the lab](lab-overview.md){ .md-button .md-button--primary }
[Download the guide (PDF)](downloads/LAB-1113-Lab-Guide.pdf){ .md-button download }
[Intro slides](downloads/LAB-1113-Lab-Intro.pptx){ .md-button download }
</div>

<!-- Add the confirmed short URL here when it is available. -->

!!! info "Use Quarkus 3.39.5"
    The lab machines have Maven dependencies for this version pre-populated. Keep the application's platform BOM and Maven plugin at **3.39.5**, even if the installed Quarkus CLI reports a different version.

## Before you start

Lab machines are pre-provisioned with IBM Bob, Podman, the Quarkus CLI, JBang, and SDKMAN with Java and Maven. Sign in to Bob with your IBMid, then [verify your environment](exercises/environment.md).

| Requirement | Check |
| --- | --- |
| IBM Bob | Launch the IDE and sign in with IBMid |
| Java | `java -version` |
| Maven 3.9.x | `mvn -version` and `sdk current maven` on lab machines |
| Quarkus CLI | `quarkus -v` |
| JBang | `jbang --version` and `which jbang` |
| Podman | `podman info` |

Open an **empty lab workspace** in Bob. You create the application during the lab.

## Lab path

Work through the core exercises in order. Each includes prompts to paste into Bob, commands to verify the result, and a **Stop and verify** checkpoint.

| Step | Time | Result |
| --- | --- | --- |
| [0. Check the environment](exercises/environment.md) | 10 min | Bob signed in; tools and paths verified |
| [1. Configure MCP and Bob](exercises/configure-mcp.md) | 15 min | Connected MCP server and workspace instructions |
| [2. Create the REST service](exercises/rest-service.md) | 15 min | Tested JSON greeting endpoint |
| [3. Add persistence](exercises/persistence.md) | 22 min | PostgreSQL-backed greetings API |
| [4. Containerize with Podman](exercises/containers.md) | 22 min | Application and database running in containers |
| [5. Wrap up](exercises/wrap-up.md) | 6 min | Review the workflow and final artifacts |
| [Optional: Add a Qute UI](exercises/qute-ui.md) | ~15 min extra | Server-rendered page and greeting form |

**Core lab: approximately 90 minutes.** Skip the optional UI if time is short.

## Keep these nearby

- [Troubleshooting](troubleshooting.md): sign-in, Maven, MCP, tests, Dev Services, and Podman recovery.
- [Downloads](downloads.md): PDF guide, introduction slides, MCP configuration, and workspace instructions.
- [Lab overview](lab-overview.md): outcomes, responsibilities, and the terms used in the exercises.
- [Source repository](https://github.com/myfear/techxchange-2026-quarkus-bob-lab-1113): source pages and publishing workflow.

The screenshots come from the verified run in IBM Bob 2.2.1 on macOS with Quarkus Agent MCP 1.0.10. Lab VMs can have different window controls and paths; follow the values for your machine.
