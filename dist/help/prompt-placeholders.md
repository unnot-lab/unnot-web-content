# Prompt and placeholders

The **Instructions** field in the agent editor is the prompt sent to the LLM. It can contain **placeholders** — references to input fields, output fields, tools, subagents, resources, and comments. In the editor you see them as **chips** (compact bars); the raw placeholder syntax is visible if you copy the prompt into a plain text editor. You can type placeholders yourself using the format below, or insert them from the UI.

## Placeholder format

Placeholders use the form **`{@type:id}`** or **`{@type:id:displayName}`**. Use type **`agent`** for a **subagent** (the **Subagents** section in the side panel):

| Type | Format | Example |
|------|--------|---------|
| **input** | `{@input:fieldName}` or `{@input:path.to.nested}` | `{@input:url}`, `{@input:user.city}` |
| **output** | `{@output:fieldName}` | `{@output:summary}` |
| **tool** | `{@tool:serverId.toolName:Display Name}` or `{@tool::name}` | `{@tool:files.read_file:Files 🠚 read_file}` |
| **agent** | `{@agent:agentId:Display Name}` or `{@agent::name}` | `{@agent:abc123:My Nested}` |
| **resource** | `{@resource:name}` | `{@resource:template.txt}` |
| **comment** | `{@comment:any text}` | `{@comment:Optional step}` — removed from the final prompt, visible only in the editor |

- **input / output** — use a dot for nested fields: `{@input:address.city}`. The **id** must match the field name (or path) from Input/Output in the side panel.
- **tool** — **id** is `serverId.toolName` (one tool) or just `serverId` (all tools of that server). Optional **displayName** after the second colon (e.g. `Server 🠚 tool_name`).
- **agent** — **id** is the subagent id (or empty for name lookup). Optional **displayName** after the second colon.
- **comment** — any text; it is **not** sent to the model, only shown in the editor.

**Legacy:** Prompts saved before the rename may still contain **`{@solution:...}`**. The app reads those as **`{@agent:...}`** (same meaning). For **new** prompts, use **`{@agent:...}`** only.

**Special characters** in field or resource names (e.g. `.`, `:`, `{`, `}`, `\`) must be escaped with a backslash: `\.` `\:` `\{` `\}` `\\`. So a field literally named "Address.city" would be `{@input:Address\.city}`. The point that separates path parts (e.g. `user.city`) is **not** escaped.

## How to insert placeholders

- **Autocomplete** — in the Instructions field, type **`/`** to open the list of inputs, outputs, tools, subagents, and resources; choose an item to insert its placeholder.
- **Double‑click in the side panel** — in **Input/Output**, **Tools**, **Subagents**, or **Resources**, double‑click a row (field, tool, subagent, resource) to insert the corresponding placeholder at the cursor in the prompt.

You can also type the placeholder text manually if you follow the format above.

## What the model sees (at execution)

When the agent runs, placeholders are **replaced** before the prompt is sent to the model:

- **input** → the value provided for that field (or `<not provided>` if empty).
- **output** → `<fieldPath>` (e.g. `<summary>`, `<result.title>`) so the model knows which output field to fill.
- **tool** / **agent** → `[tool: name]` or `[Server Name]` so the model sees which tools (and subagents) it can call.
- **resource** → `[resource: name]`.
- **comment** → removed (empty string).

So in the editor you work with `{@input:url}` and `{@tool:...}`; the model receives concrete values and markers like `[tool: read_file]` and `<summary>`.

## Editor mode: Markdown vs Text

The Instructions field can be **Markdown** (headings, bold, lists, code) or **plain text**. The app **auto‑detects** the format from the content and switches the editor accordingly: in **MD** mode you see rendered formatting and placeholder chips; in **TXT** mode you see raw Markdown and the literal placeholder text (e.g. `{@input:url}`).

You can **switch manually** with the **MD** / **TXT** button on the toolbar above the Instructions field: **MD** = Markdown view, **TXT** = plain text / source view. Use TXT when you want to see or edit the exact placeholder syntax.

For a first agent, see [Your First Agent](/help/getting-started/first-agent). For the full agent workflow, see [Agents](/help/agents).
