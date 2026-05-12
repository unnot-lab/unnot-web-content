# CLI

The Unnot CLI (`unnot` command) lets you call the Unnot API from the command line: list agents and tools, run an agent, get execution results or logs, and manage tools. The **Unnot app must be running**; the CLI connects to it over HTTP (localhost by default). Run **`unnot --help`** for the exact command list.

## Where the CLI lives

The CLI files are in the **`cli`** folder **inside your Unnot installation folder** (next to the main application executable).

**Default locations** (when you accept the installer’s suggested folder):

- **Windows (install for current user):** `%LOCALAPPDATA%\Programs\Unnot\cli\` — for example `C:\Users\<YourName>\AppData\Local\Programs\Unnot\cli\`
- **macOS:** inside the Unnot app bundle (e.g. under **Unnot.app** → **Show Package Contents** → look for the **`cli`** folder next to the packaged app files)
- **Linux:** **`cli`** next to the unpacked application files (or inside the layout provided by your package)

If you **chose a custom installation directory** in the installer, open that folder instead — the **`cli`** folder is always **next to the Unnot application**, not under your documents or settings.

**All users / Program Files (Windows):** If your organization installed Unnot “for all users,” the program (and **`cli`**) may be under **`Program Files`** rather than `%LOCALAPPDATA%\Programs\Unnot`. Use the **`cli`** folder beside **`Unnot.exe`** in that installation.

## Running the CLI

- **Windows:** run **`unnot.cmd`** (double-click works for quick tests; a terminal is better for automation).
- **macOS / Linux:** run the **`unnot`** script in the **`cli`** folder.

You can:

1. Run **`unnot.cmd`** / **`unnot`** by **full path**, or
2. Add the **`cli`** folder to your system [PATH](https://en.wikipedia.org/wiki/PATH_(variable)), then open a **new** terminal and type **`unnot`**.

**Windows — add to PATH (one-time), default install path:**  
In Command Prompt:

`setx PATH "%PATH%;%LOCALAPPDATA%\Programs\Unnot\cli"`

Then open a new terminal. If Unnot was installed elsewhere, substitute your real **`cli`** folder path instead of `%LOCALAPPDATA%\Programs\Unnot\cli`.

**Quick test without changing PATH:**

`"%LOCALAPPDATA%\Programs\Unnot\cli\unnot.cmd" --help`

(Again, replace the path if your install folder is different.)

Use **`unnot --help`** or **`unnot -h`** to print the command list.

## How the CLI finds the API

The CLI picks the API base URL in this order:

1. **`UNNOT_API_URL`** — if set (for example `http://localhost:20222`), that address is used. Use the host and port only (do not append `/api/v1`).
2. **`api-port.json`** — in the Unnot **app data** directory (not the install folder): on Windows **`%APPDATA%\unnot`**, on macOS **`~/Library/Application Support/unnot`**, on Linux **`~/.config/unnot`** or **`$XDG_CONFIG_HOME/unnot`**. The file contains something like `{"port": 20222}`. If the default port was busy, the app writes the actual port here.
3. **Default** — `http://localhost:20222`.

If the app uses a non-default port, either set **`UNNOT_API_URL`** or run the CLI on the **same machine** as the app so it can read **`api-port.json`**.

## Command overview

Format is **verb noun**; for agent and tool commands, `<id>` can be the agent/tool **id** or **exact name**. To find an id in the app: in **Agents** or **Tools** enable the **ID** column via the Columns button, or open the item in the editor → **Agent Info** / **Server Info** → **More**.

| Command | Description |
|--------|-------------|
| `unnot list agents [--limit n]` | List agents (JSON). |
| `unnot list executions [--status ...] [--agent-id id] [--limit n]` | List executions; filter by status or agent. |
| `unnot list tools [--limit n]` | List MCP tools (JSON). |
| `unnot list agent-categories` | List agent categories. |
| `unnot search agents <query> [--limit n]` | Search agents by query. |
| `unnot search tools <query> [--limit n]` | Search tools by query. |
| `unnot get agent <id> [schema \| status \| scheduler \| llm \| statistics]` | Get agent, schema, status, scheduler, LLM info, or statistics. |
| `unnot run agent <id> [--input k=v ...] [--no-wait]` | Run agent; optional inputs; `--no-wait` returns without waiting for completion. Alias: `unnot run <id>`. |
| `unnot get execution <id> result \| logs \| chat [--limit n] [--include-thinking]` | Get execution result, logs, or chat. |
| `unnot get tool <id> [status \| functions \| logs]` | Get tool, status, functions, or logs. |
| `unnot get tool <id> function <name> schema` | Get argument schema for a tool function. |
| `unnot run tool <id> function <name> [--arg k=v ...] [--json {...}]` | Call a tool function with arguments. |
| `unnot connect tool <id>` | Connect (start) the MCP tool. |

Output is JSON to stdout. Redirect or pipe as needed (e.g. `unnot list agents | jq`).

## Examples

List agents and run one by name:

```bash
unnot list agents
unnot run agent "My Daily Report" --no-wait
```

Run with input parameters (key=value):

```bash
unnot run agent "Summarizer" --input url="https://example.com/article" --input max_length=500
```

Get execution result after a run (use the execution id returned by run, or list executions):

```bash
unnot list executions --agent-id "My Daily Report" --limit 1
unnot get execution <EXECUTION_ID> result
```

List tools and connect one:

```bash
unnot list tools
unnot connect tool "My MCP Server"
```

## Typical scenarios

- **Script or scheduled task** — start the app (or assume it’s already running), then call `unnot run agent <id>` or `unnot run agent <id> --no-wait` and later poll `unnot get execution <id> result`.
- **Check status** — `unnot get agent <id> status`, `unnot get agent <id> scheduler`, `unnot get tool <id> status`.
- **Remote machine** — set **`UNNOT_API_URL`** to `http://<host>:<port>` (and enable **Allow remote API access** in Settings on the computer where Unnot runs). Prefer a trusted network or HTTPS via reverse proxy.

For connection issues, see [Troubleshooting](/help/troubleshooting). For HTTP paths and examples without the CLI, see [REST API](/help/automation/rest-api).
