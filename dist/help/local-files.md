# Local files and folders

Agents often work with files on your machine. To let the model use a file or folder, **declare the path** in the agent setup—not only by typing a path in **Instructions**. Unnot offers **three** ways to do that; pick the one that matches your situation.

**Quick guide**

- **Different file each run** — **Input/Output** → **File path** or **Folder path**; fill the path when you **Run** (or via API); reference it with `{@input:fieldName}` in the prompt.
- **Same files for this agent** (templates, knowledge base) — **Resources** → `{@resource:name}` in the prompt.
- **Write files, browse undeclared paths, or special formats** — attach an MCP tool in **Tools** (e.g. Filesystem, Office).
- **Run when a folder changes** — **File system** trigger (see [File system trigger](#file-system-trigger) below).

## At a glance

| You have… | Configure in… | What you get |
|-----------|---------------|--------------|
| A path per run (form, API, or file trigger) | **Input/Output** → **File path** or **Folder path** | The agent can read those declared paths automatically (usually no extra file MCP tool needed) |
| Files tied to the agent (templates, fixed folders) | **Resources** + `{@resource:name}` in the prompt | The agent can read attached resources automatically |
| Arbitrary paths, writes, or special formats | **Tools** → attach an MCP server (e.g. Filesystem, Office) | The agent uses that tool’s parameters (read, write, format-specific actions) |

**Security:** Access through input fields and resources only works for **declared** paths (input fields, trigger payload, or resources on the agent). The model cannot read random disk locations from free text in the prompt alone.

## Input fields: File path and Folder path

In the agent editor, open **Input/Output** and add a field of type **File path** or **Folder path**. When you **Run**, type a path or use **Browse**; the app can check that the path exists.

When the agent runs with at least one **File path** or **Folder path** field, it can list folders and read file contents for paths you provided in those fields (including paths from a **File system** trigger). Pasting a path only in **Instructions** without an input field does **not** grant access.

**Prompt** — use `{@input:fieldName}` (or a nested path like `{@input:Input File.filePath}`) so the model sees the path in the prompt. Do **not** use `{@resource:…}` for run-time input paths. See [Prompt and placeholders](/help/prompt-placeholders).

**Nested fields** — you can put **File path** / **Folder path** inside an object or array (e.g. several files in one run). For a **File system** trigger, add those field types on properties inside the system object **Input File**, not only at the top level.

### Chaining another MCP tool (optional)

If the agent also uses an MCP file tool, pass the full path that tool expects (parameter names come from that tool’s schema). Execution details may include a full path for declared inputs—see [Troubleshooting](/help/troubleshooting#agent-cannot-read-a-local-file) if a tool call fails. You usually do **not** need a separate Filesystem MCP tool just to **read** a path you already declared as **File path** / **Folder path**.

## Agent resources

In the side panel, open **Resources** to attach files or folders to the agent:

- **File** — **snapshot** (a copy stored with the agent; useful when you export or move the agent) or **reference** (read live from a path on your machine).
- **Folder** — snapshot or reference; reference folders are listed when the agent reads them.

In **Instructions**, reference a resource with `{@resource:name}`. At run time it becomes `[resource: name]` for the model. The agent reads those attachments through **Resources**, not through **File path** / **Folder path** input fields.

**When to use resources vs input fields**

- **Resources** — stable files for this agent (templates, knowledge base, a fixed working folder).
- **Input File path / Folder path** — path changes every run, manual **Run**, CLI/API `inputParameters`, or a **File system** trigger.

See [Prompt and placeholders](/help/prompt-placeholders) for placeholder syntax.

## Attached MCP tools

Attach tools such as **Filesystem** or domain-specific readers (e.g. spreadsheets) when declared input fields and resources are not enough: writing files, browsing paths you did not declare as inputs, or formats that need a dedicated server.

If the path is already a **File path** or **Folder path** input, you usually do **not** need a generic Filesystem tool just to read that file—the agent already has access through the input field.

See [Tools](/help/tools) for connecting MCP servers.

## File system trigger

A **File system** trigger runs the agent when watched files or folders change (create, update, etc.). Unnot adds a system input object **Input File** with details about the event (paths, action). Put **File path** or **Folder path** fields on properties inside **Input File** so the agent can read those paths the same way as other input fields.

- **Group files before run** — one run with several files in **Input File** (see [Agents](/help/agents#triggers)).
- **Batch run** — many runs, one item each, configured under **Batch** in the agent editor (see [Batch run](/help/batch-run)).

Like other non–on-demand triggers, the agent must be **Activated** for automatic runs. See [Agents](/help/agents#triggers).

## Automation (CLI and REST API)

Use this when you **start a run yourself** (CLI or `POST /agent/{id}/run`), not when a **File system** trigger fires—the trigger fills **Input File** automatically (see [File system trigger](#file-system-trigger) above).

Pass file and folder paths in **`inputParameters`** using the **same field names** as in the agent’s **Input/Output** schema (nested objects use the same structure as in the editor). To see the exact names for an agent, call `GET /api/v1/agent/{id}/schema` (or use the CLI equivalent).

Example: the agent has a top-level **File path** field named `documentPath`:

```json
{
  "waitForCompletion": true,
  "inputParameters": {
    "documentPath": "D:\\data\\report.pdf"
  }
}
```

`documentPath` is only an example—your field may be named differently. If you **manually** run an agent whose schema includes **Input File** (from a file trigger) and want to simulate trigger data without a real filesystem event, pass an **Input File** object with the same shape the app would fill (`filePath`, `event`, and other enabled subfields). That is uncommon; normal file-trigger runs do not need this.

See [REST API](/help/automation/rest-api) and [CLI](/help/automation/cli).

## See also

- [Agents](/help/agents) — triggers, activation, Input/Output panel.
- [Prompt and placeholders](/help/prompt-placeholders) — `{@input:…}` vs `{@resource:…}`.
- [Tools](/help/tools) — MCP connection and status.
- [Troubleshooting](/help/troubleshooting#agent-cannot-read-a-local-file) — if the model fails to read a file.

**Advanced:** [Agents schema](/help/schemas/unnot-agents-full.schema.json) — `subtype: file` / `folder` on string input fields.
