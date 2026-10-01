# Troubleshooting

Rescue steps for the [LAB-1113 tutorial](lab-overview.md). Work through the section that matches your symptom.

---

## Bob sign-in fails or hangs

**Symptoms:** Browser auth never completes; Bob stays on the sign-in screen; repeated login prompts.

**Checks:**

1. Confirm outbound HTTPS is allowed to `login.ibm.com` on lab networks. See [Configuring firewall rules](https://docs.bob.ibm.com/docs/ide/troubleshooting/ts-firewall-rules) in the Bob docs.
2. If you use a corporate proxy, see [Configuring proxy settings](https://docs.bob.ibm.com/docs/ide/troubleshooting/ts-proxy).
3. Confirm your IBMid works in a normal browser at [ibm.com](https://www.ibm.com).
4. Quit and relaunch Bob after successful browser authentication.

**Escalate:** Ask a lab facilitator to verify network allowlists for IBMid endpoints.

---

## Maven not found (SDKMAN)

**Symptoms:** `mvn: command not found` after opening a new terminal.

**Fix:**

```bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
mvn -version
sdk current maven
```

If Maven is not installed:

```bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk list maven
sdk install maven 3.9.9
```

Only self-install if your lab image documentation allows it. Otherwise ask a facilitator.

**Prevention:** Add SDKMAN init to your shell profile so new Bob terminal sessions source it automatically:

```bash
# Typically already present in ~/.bashrc or ~/.zshrc on lab images
export SDKMAN_DIR="$HOME/.sdkman"
[[ -s "$HOME/.sdkman/bin/sdkman-init.sh" ]] && source "$HOME/.sdkman/bin/sdkman-init.sh"
```

---

## MCP tools never appear

**Symptoms:** Bob does not propose Quarkus MCP tools; no `quarkus-agent` in MCP settings.

**Checks:**

1. **Settings → MCP** — **Enable MCP tools for new tasks** must be enabled (**Use MCP Servers** in earlier builds).
2. Config file must be **`.bob/mcp.json`** in your workspace root (not `mcp.json` elsewhere).
3. JSON must be valid. `command` and `JAVA_HOME` must be **absolute paths**, not the placeholders `REPLACE_WITH_JBANG_PATH` / `REPLACE_WITH_JAVA_HOME`. Compare with [mcp-quarkus-agent.json](downloads/mcp-quarkus-agent.json).
4. Restart the `quarkus-agent` server from the MCP tab (renew icon).
5. Review individual tool requests in **Permissions** (**Auto-Approve** in earlier builds). Auto-approval is not required for tools to exist.

**Recovery prompt** (Agent mode):

```text
List all MCP servers you are connected to and their available tools.
```

---

## Quarkus Agent MCP server fails to start

**Symptoms:** `quarkus-agent` shows an error in MCP settings; server restart fails; `spawn jbang ENOENT`.

Bob launched from the desktop inherits the **system PATH**, not the SDKMAN PATH from `~/.bashrc` / `~/.zshrc`.

**Checks:**

```bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
which jbang
echo "$JAVA_HOME"
jbang --version
jbang quarkus-agent-mcp@quarkusio --help
```

1. Put the `which jbang` path in `.bob/mcp.json` as `command`, and set `env.JAVA_HOME` to the Java home directory. See [Configure the Quarkus Agent MCP](exercises/configure-mcp.md).
2. **First run download** — JBang needs network access to fetch `quarkus-agent-mcp@quarkusio`. Retry after confirming connectivity.
3. **Restart server** — MCP tab → locate `quarkus-agent` → restart.

Enable MCP agent logging if problems persist:

- Set `"AGENT_MCP_LOG_ENABLED": "true"` in the MCP server `env` block in `.bob/mcp.json`, or ask Bob to call `quarkus_agent_log_enable` after the server is running.

---

## Bob skips quarkus_skills

**Symptoms:** Bob edits REST, Panache, or Qute code without calling `quarkus_skills` first.

**Recovery prompt:**

```text
Stop. Call quarkus_skills for the relevant extensions before making any further code changes. Then continue with the lab task.
```

The checkpoints in [Add AGENTS.md for Quarkus MCP](exercises/configure-mcp.md#add-agentsmd-for-quarkus-mcp) and [Add a JSON greeting endpoint](exercises/rest-service.md#add-a-json-greeting-endpoint) depend on this habit. Confirm the workspace-root `AGENTS.md` is present and start a **new chat** if Bob still skips MCP.

## Maven wrapper rejected by MCP

**Symptom:** Creation succeeds, but startup reports that `mvnw` is not tracked by Git.

The lab uses the preinstalled system Maven. Set `noWrapper: true` in the create prompt so MCP does not select an untracked wrapper. In the tested MCP server, `buildTool: "maven"` alone still selected an existing wrapper.

If the project already contains wrapper scripts, ask Bob to move `mvnw` and `mvnw.cmd` to a backup directory outside the application, then call `quarkus_start`. Keep the application source and POM. Do not commit files or run dev mode manually to resolve this error.

## JSON endpoint returns a non-JSON response

**Symptom:** `/api/greeting` returns text instead of the expected JSON object, or REST Assured reports a JSON parsing error.

The `rest` extension alone does not supply Jackson serialization. Ask Bob to add `rest-jackson` through the MCP extension tools, stop and start through MCP to resolve the new dependency, and rerun the tests. Keep Quarkus at 3.39.5.

---

## Port 8080 already in use

**Symptoms:** Dev mode starts on a different port; curl to 8080 fails; the app container cannot bind 8080.

**Checks:**

1. Read the URL Bob reports after `quarkus_create` or `quarkus_start`.
2. Quarkus can assign another port when 8080 is taken.
3. Ask Bob: *What port is greeting-service running on?*
4. Before `podman run -p 8080:8080`, confirm nothing else is listening: leftover **dev mode** still holds 8080 until `quarkus_stop`.

**Recovery:**

```text
Use quarkus_status or quarkus_logs to tell me which HTTP port the app is using.
```

Use that port in curl commands and browser URLs.

---

## Tests fail or runner stuck

**Symptoms:** MCP test tools return errors; "Tests already in progress" message.

Do **not** run `mvn clean` while dev mode is running. It deletes `target/test-classes` and breaks the MCP test runner.

**Recovery:**

1. Ask Bob to call `devui-exceptions_getLastException` via `quarkus_callTool` for structured error details.
2. After fixing the code, call `devui-exceptions_clearLastException`.
3. If the runner is stuck, ask Bob to run `quarkus_stop` then `quarkus_start` through MCP.

**Recovery prompt:**

```text
Tests are failing. Use devui-exceptions_getLastException via Quarkus MCP, fix the issue, re-run tests via MCP, then clear the exception.
```

---

## Dev Services PostgreSQL does not start

**Symptoms:** App fails with datasource errors in dev mode; no Postgres container in `podman ps`; logs mention a missing JDBC URL.

**Checks:**

1. `jdbc-postgresql` is on the project classpath.
2. `quarkus.datasource.jdbc.url` is **not** set in the default (unprefixed) config. A `%prod.` URL is expected later; an unprefixed URL disables Dev Services.
3. `podman info` succeeds.
4. Ask Bob to call `quarkus_logs` and look for Dev Services / PostgreSQL startup.

**Recovery prompt:**

```text
Dev Services did not start PostgreSQL. Show me the datasource properties, remove any
unprefixed jdbc.url, call quarkus_skills for the datasource / Panache extensions,
then restart with quarkus_stop and quarkus_start.
```

---

## Persist or @Transactional errors

**Symptoms:** POST `/api/greetings` returns 500; logs mention `TransactionRequiredException` or a closed persistence context.

**Fix:** Write operations that call `persist()` must run inside a transaction. Ask Bob to put `@Transactional` on the POST (or service) method, re-read Panache skills, and re-run tests through MCP.

```text
POST /api/greetings failed with: <paste error>. Call quarkus_skills for Panache,
use devui-exceptions_getLastException, add @Transactional on the write path,
re-run tests via MCP, then clear the exception.
```

---

## Podman errors

**Symptoms:** `podman info` fails; permission denied; cannot run containers.

**Checks:**

```bash
podman info
podman version
```

1. **Service not running** — start the Podman socket or service per your lab image documentation.
2. **Rootless setup** — lab images should be preconfigured; ask a facilitator if `podman info` reports configuration errors.
3. **Port mapping** — use the host port from Bob's `podman run` command, not always 8080.

**Recovery prompt:**

```text
podman info failed with: <paste error>. Diagnose and suggest the fix for this lab environment. Do not install Docker.
```

---

## Container cannot reach PostgreSQL

**Symptoms:** App container logs show `Connection refused` or `UnknownHostException` for the database; POST to the published port fails after `podman run`.

**Checks:**

1. Both containers are on the same network (`podman network inspect greeting-net`).
2. The JDBC URL host matches the **database container name** (for example `greeting-db`), not `localhost`.
3. Datasource username, password, and database name match the `POSTGRES_*` environment variables.
4. `%prod.quarkus.hibernate-orm.schema-management.strategy=drop-and-create` is set for this lab so tables exist on first boot.
5. Postgres finished starting before the app; wait and `podman restart greeting-app` if needed.

**Recovery prompt:**

```text
The greeting-app container cannot reach PostgreSQL. Show the %prod datasource
properties, the podman network, and podman logs for greeting-db and greeting-app.
Fix hostname, credentials, and schema-management.strategy. Do not install Docker.
```

---

## Plan and implementation conflated

**Symptoms:** Bob implements while still in planning mode, or implementation ignores the plan.

**Fix:**

1. Finish Plan mode and review `greeting-service/docs/ui-plan.md`.
2. Click **+** (New chat) to start a fresh conversation.
3. Switch to Agent mode.
4. Paste the implementation prompt from [Optional: Plan a Qute UI](exercises/qute-ui.md).

---

## Agent vs Advanced mode naming

Some Bob builds show **Agent**; others show **Advanced** for the mode that can edit files, run commands, and use MCP. Use whichever label your build displays. They are equivalent for this lab.

---

## Wrong workspace

**Symptoms:** `.bob/mcp.json`, `AGENTS.md`, or `greeting-service/` not where you expect.

**Fix:** All lab work belongs in `~/lab-1113-workspace` (or your chosen empty folder), opened via **File → Open Folder**. Put `AGENTS.md` and `.bob/mcp.json` in that folder's root. `greeting-service/` is created later, in the same folder, by `quarkus_create`. Do not run the tutorial inside the documentation repository clone unless you intentionally use it as your workspace.

---

## Still stuck?

1. Note the exact error message and which lab section you are in.
2. Start a **new chat** and paste the error with: *I am on LAB-1113. Here is the error. Suggest the smallest fix.*
3. Ask a lab facilitator if the issue is environmental (network, Podman, missing SDKMAN candidates).
