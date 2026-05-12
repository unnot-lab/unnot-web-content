# REST API

The Unnot app exposes a **REST API** under **`/api/v1`** so you can list agents and tools, run agents, and get execution results from any HTTP client (curl, scripts, other applications). URL paths use the **`agent` / `agents`** segments (not “solution”). The API runs on the **same port** as the app; the **app must be running**. There is no separate API server process. Implementation: `src/transport/rest-api/restHandler.ts` (thin wrapper over `assistantTools.executeTool`).

## Base URL and port

- **Base URL:** `http://localhost:<port>/api/v1`
- **Port:** default **20222**. If that port is busy at startup, the app uses the next free port. Check **Settings → General** (API port) or the `api-port.json` file in the app data directory.
- **Example:** `http://localhost:20222/api/v1`

All paths below are relative to that base (e.g. `GET /agents` → `GET http://localhost:20222/api/v1/agents`).

## Remote access

By default the API listens only on **localhost**. To call it from another computer:

1. Open **Settings → General**.
2. Enable **Allow remote API access**.
3. Restart the app.

Then use `http://<this_computer_ip>:20222/api/v1` (or the actual port). **Traffic is not encrypted.** Use only on a trusted network or put a reverse proxy with HTTPS (e.g. Caddy, nginx) in front. See [Troubleshooting](/help/troubleshooting#cli-or-api-connection-refused-or-cannot-connect).

## Example requests

Replace `AGENT_ID` with the agent id or exact name; use the port your app is actually using. To find an id in the app: in **Agents** or **Tools** enable the **ID** column (Columns button), or open the item in the editor → **Agent Info** / **Server Info** → **More**.

**List agents** *(HTTP: `GET /agents`)*

```bash
curl -s "http://localhost:20222/api/v1/agents" | jq
```

**Get agent status** — readiness and execution state (in the app UI: **Ready**, **Not configured**, **Error**, **Running**; some automation or query parameters may still use the slug `not_ready` for the not-configured state)

```bash
curl -s "http://localhost:20222/api/v1/agent/AGENT_ID/status" | jq
```

**Run agent** (without waiting for completion)

```bash
curl -s -X POST "http://localhost:20222/api/v1/agent/AGENT_ID/run" \
  -H "Content-Type: application/json" \
  -d '{"waitForCompletion": false}' | jq
```

**Run agent** (wait for completion; can be slow)

```bash
curl -s -X POST "http://localhost:20222/api/v1/agent/AGENT_ID/run" \
  -H "Content-Type: application/json" \
  -d '{"waitForCompletion": true, "inputParameters": {"key": "value"}}' | jq
```

**Get execution result** (use the execution id returned by the run, or from listing executions)

```bash
curl -s "http://localhost:20222/api/v1/execution/EXECUTION_ID/result" | jq
```

**List tools**

```bash
curl -s "http://localhost:20222/api/v1/tools" | jq
```

**List executions** (optional query: **`agentId`**, `status`, `limit`, `offset`, `sortBy`, `sortOrder`)

```bash
curl -s "http://localhost:20222/api/v1/executions?agentId=AGENT_ID&limit=10" | jq
```

On Windows (PowerShell) you can set a variable and use `Invoke-RestMethod`:

```powershell
$base = "http://localhost:20222/api/v1"
Invoke-RestMethod -Uri "$base/agents" -Method Get
Invoke-RestMethod -Uri "$base/agent/MY_AGENT_ID/status" -Method Get
Invoke-RestMethod -Uri "$base/agent/MY_AGENT_ID/run" -Method Post -Body '{"waitForCompletion":false}' -ContentType "application/json"
```

## Main endpoints (overview)

| Area | Examples |
|------|----------|
| **Agents** | `GET /agents`, `GET /agents/search?q=...`, `GET /agents/categories`, `GET /agent/{id}`, `GET /agent/{id}/schema`, `GET /agent/{id}/status`, `GET /agent/{id}/scheduler`, `GET /agent/{id}/llm`, `GET /agent/{id}/statistics`, `POST /agent/{id}/run` |
| **Executions** | `GET /executions` (query `agentId`, …), `GET /execution/{id}/result`, `GET /execution/{id}/logs`, `GET /execution/{id}/chat` |
| **Tools** | `GET /tools`, `GET /tools/search`, `GET /tool/{id}`, `GET /tool/{id}/status`, `POST /tool/{id}/connect`, `GET /tool/{id}/functions`, `GET /tool/{id}/logs`, … |

Path parameters: `{id}` can be the object’s id or, where supported (agent, tool), its exact name. To see the id in the app: enable the **ID** column in the Agents or Tools table (Columns button), or in the editor open **Agent Info** / **Server Info** and click **More**. Query parameters vary by endpoint (e.g. `limit`, `category`, `status`, `q` for search).

**Using names in the URL:** When you use a **name** (e.g. an agent name with spaces like `Weather Check`) in the path or in query parameters, it must be **URL-encoded**. Otherwise the path is cut at the first space and the server receives only part of the name. Encode spaces as `%20`, parentheses as `%28` and `%29`. Example: `Weather Check` → `Weather%20Check`. Encode query values the same way (e.g. `?agentId=Weather%20Check`).

For the full list of endpoints, request/response shapes, and query parameters, see **`docs/rest-api-design.md`** in the repository (must match `restHandler.ts`).

## Security

- The API uses **HTTP only** (no TLS). Do not expose it directly to the internet or untrusted networks.
- With **Allow remote API access** on, anyone on the same network can call the API. Use a firewall, VPN, or reverse proxy with HTTPS if you need safer remote access.

For automation overview and CLI, see [Automation (CLI & API)](/help/automation) and [CLI](/help/automation/cli). For port and connection issues, see [Troubleshooting](/help/troubleshooting).
