# Downloads

Use the web exercises for copyable prompts and commands. Keep the PDF guide and introduction slides available for reference.

| File | Purpose |
| --- | --- |
| [Lab guide (PDF)](downloads/LAB-1113-Lab-Guide.pdf){ download } | Final 28-page LAB-1113 guide, including the lab-run screenshots |
| [Introduction slides (PowerPoint)](downloads/LAB-1113-Lab-Intro.pptx){ download } | Six-slide introduction to the lab |
| [Workspace instructions (AGENTS.md)](downloads/AGENTS.md.txt){ download="AGENTS.md" } | Quarkus MCP workflow rules for IBM Bob |
| [MCP configuration (JSON)](downloads/mcp-quarkus-agent.json){ download="mcp.json" } | Template for the workspace's `.bob/mcp.json` |

## Use the workspace templates

1. Open your empty lab workspace in Bob.
2. Save the instructions as `AGENTS.md` at the workspace root. If your browser keeps the `.txt` suffix, remove it.
3. Save the JSON template as `.bob/mcp.json` in the same workspace.
4. Replace `REPLACE_WITH_JBANG_PATH` and `REPLACE_WITH_JAVA_HOME` with the absolute paths from your machine.
5. Follow [Configure the Quarkus Agent MCP](exercises/configure-mcp.md) to connect the server, review approvals, and start a new task.

These templates belong in your lab workspace, alongside the `greeting-service` directory you create during the REST exercise.
