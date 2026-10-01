# Lab overview

Modern Java development is no longer just about writing code. It is about building, validating, and running applications in environments that match production from the start.

This lab brings those concerns together with **Quarkus** and **IBM Bob**. You use Bob to guide decisions, generate structure, and improve the codebase as it evolves. The Quarkus Agent MCP gives Bob the project lifecycle, extension patterns, and documentation lookup that a generic chat session does not have.

By the end, you will have a working Quarkus application running in a container, and a practical sense of how an AI-assisted workflow reduces friction between development and operations.

!!! note "Bob output varies"
    Class names, file paths, and port numbers can differ between runs. Verify **behavior**: endpoints respond, tests pass, data survives a second request, and the container serves the API. Do not require an exact diff match to the snippets in this tutorial.

## What you will have at the end

- `.bob/mcp.json` with the Quarkus Agent MCP configured for this workspace
- `AGENTS.md` at the workspace root so Bob uses Quarkus MCP on every conversation
- A `greeting-service` Quarkus app created and operated through MCP tools
- REST endpoints that create and list greetings
- PostgreSQL persistence: Dev Services in development, a Postgres container next to the app image
- A Podman image `localhost/greeting-service:lab` serving the API

## How Bob, MCP, and you divide the work

The naive approach is to paste Maven commands into chat and hope Bob guesses Quarkus patterns.

The working approach for this lab:

- **Quarkus Agent MCP** owns project create, dev mode, tests, exception lookup, and documentation search.
- **`quarkus_skills`** supplies extension-specific patterns. Bob must read skills **before** writing Quarkus code.
- **Bob** orchestrates bounded edits and tool calls.
- **You** approve tool use, review the plan and the files, and prove behavior with `curl` and Podman.

If Bob skips MCP and types `mvn quarkus:dev` or `mvn test` by hand, stop it and point it back at the tools. Raw Maven is reserved for packaging the container image after you stop dev mode.

## Time budget

| Step | Section | Mode | Time |
|------|---------|------|------|
| 0 | Check the lab environment | — | 10 min |
| 1 | Configure Quarkus Agent MCP and AGENTS.md | Agent | 15 min |
| 2 | Create the REST service | Agent | 15 min |
| 3 | Add persistence | Agent | 22 min |
| 4 | Containerize with Podman | Agent | 22 min |
| 5 | Wrap-up | — | 6 min |

**Total:** about 90 minutes. If you run long, skip the [optional Qute stretch](exercises/qute-ui.md).

## Terms you will use

**MCP** (Model Context Protocol) is how Bob connects to external tools. For more information, see [Understanding MCP](https://docs.bob.ibm.com/docs/ide/configuration/mcp/understanding-mcp).

**Quarkus Agent MCP** is a local JBang server that exposes `quarkus_create`, `quarkus_skills`, `quarkus_searchDocs`, `quarkus_callTool`, and related tools. It stays up even if the app crashes.

**Skills** are extension-specific patterns and testing guidance. Call `quarkus_skills` before writing REST, Panache, or container-image code.

**Dev Services** start a database container automatically in *dev* and *test* when you add a JDBC extension and do **not** set a datasource URL. Quarkus does not start Dev Services in *prod*. Your container run must supply a real database.

**Agent mode** lets Bob edit files, run commands, and call MCP tools. Some Bob builds label this **Advanced**. The capability is the same.

**AGENTS.md** is the file Bob reads at the start of every conversation. It gives Bob persistent project instructions. For more information, see [Start a project with /init and AGENTS.md](https://bob.ibm.com/docs/ide/tutorials/start-a-project).

**New Task (+)** (called **New chat** in some builds) starts a fresh conversation and resets the context window. Use it after you add `AGENTS.md`, and when you switch from planning to implementation. For more information, see [Create a new context window](https://docs.bob.ibm.com/docs/ide/getting-started/tutorials/context-window).
