# AI Assistant

The AI Assistant helps you configure and manage agents **and MCP tools** by chat instead of clicking through every form. There is also a separate **Chat** entry for one-off requests that do not create or edit an agent.

## Where to open it

- **Chat** (sidebar) — one-off help: ask a question, attach a file, search/install MCP tools (with your confirmation), run tool functions, and **run an existing agent** when you ask. To turn a chat into a reusable agent, Chat can offer **Open Agent Assistant**; to update an existing agent (name/description/category, tools, simple trigger, scheduler on/off, prompt, run limits), it can show a compact **Apply** card (you confirm); to set up an MCP tool in the editor, **Open Tool Assistant** (you confirm). Model settings are the same as for the AI Assistant (**Settings → AI Assistant**).
- **Agent editor** — robot / AI Assistant button in the toolbar. The chat is about the agent you are editing.
- **Tool editor** — AI Assistant button in the toolbar (also for a new unsaved tool: the first message saves it, then the chat continues). The chat is about the MCP tool you are editing: settings, connection errors, runtime packages (`uv` / `npx`), and filling missing parameters.

## What Chat can do (one-off)

- Answer questions and draft text from what you paste or attach.
- Read chat attachments (and use session MCP tools when a format needs them, e.g. spreadsheets).
- Search installed tools, inspect schemas, connect, and run a function.
- Search the Tool Gallery and offer install via a UI card (you confirm; no silent install).
- Look up existing agents by name/id (read-only) and offer an **Apply** card to patch identity, tools, simple schedule/interval, scheduler activation, prompt, or execution settings.
- **Run** an existing agent from Chat (`agent_run`) when you ask — same as running from the agent editor.
- Offer an **Apply** card to patch an existing MCP tool (name/description/category, command/args/env, url, enabled). Secrets/OAuth, retries/timeouts, and deep setup stay in **Tool Assistant** (Chat can open the **existing** tool there — not a new draft).
- Look up in-app help (`help` tools).

Chat will **not** silently create or edit agents or tools. If you ask to save/automate as an agent or set up a tool in the editor, it can show a card to open **Agent Assistant** or **Tool Assistant**. If you ask to improve an **existing** agent, it can show an **Apply / Skip** patch card (identity, tools, simple trigger, scheduler, prompt/settings) — nothing is written until you Apply. Complex edits (file watchers, batch, input schemas) stay in **Agent Assistant**.

## What the assistant can do (agents)

- **Edit the current agent** — the one open in the editor. It can change the prompt (instructions), add or remove tools, edit input and output fields, set triggers, resources, LLM model, execution settings, and activate or deactivate the scheduler. You describe what you want; the assistant applies the changes.
- **Run and monitor runs** — run the current agent or **any other agent** by name or id (with optional input parameters). It can wait for completion or run in the background. It has access to **execution history**: list runs, open a specific run’s **result**, **logs** (by phase and level), and **chat** (messages and tool calls). So you can ask “run this”, “show the last run”, “what went wrong in execution X?” and get real data.
- **Browse agents and tools** — list or search agents and tools, get full details of any agent (read-only). Useful to attach a tool to the current agent, run another agent, or compare settings. It can also see tool status, connection logs, and call a tool function (e.g. for testing).

## What the assistant can do (tools)

- **Inspect the current tool** — config, readiness, connection status, gallery origin.
- **Diagnose failures** — analyze connection errors and suggest fixes (including missing `uv` / `npx` / Node on PATH).
- **Update settings** — command, args, env, URL, auth-related fields, and per-tool timeouts / sessions / tool-call retries (secrets: prefer entering them yourself or confirming when asked).
- **Connect / retry** — start the server and report what failed.
- **Gallery** — search catalogs; for the open tool, prefer **Apply to this tool** (updates config, name, origin). Or install an **additional** tool via the UI card. Does not silently install.

## What the assistant cannot do

- **Edit another agent from the agent editor chat** — the Agent Assistant cannot change an agent that is not currently open. To change another agent there, open it in the editor first. (**Chat** can propose a patch to a named agent via an Apply card.) It *can* read any agent and run any agent.
- **Edit another tool from the tool chat** — write operations apply only to the tool open in the Tool Editor.
- **Install packages on your machine** — it can recommend installing `uv`, Node/npm, etc.; you install them yourself.

## Settings

The model used by the assistant (and its options) is configured in **Settings → AI Assistant**. That is separate from the model chosen for an agent’s execution. The same settings apply in both the agent and tool editors. Session MCP extras (e.g. web search) use the same default list.

## Saving and rollback

When you send your **first message** that might change the agent or tool (and there are unsaved changes), the app **saves automatically** before sending. That creates a checkpoint so you can undo later.

- **In Chat** (sidebar) — roll back to a previous user message to undo later replies (chat history only; nothing else in the app is restored).
- **In the agent editor chat** — roll back the conversation; optionally restore the agent to the state at that message (or undo the chat only).
- **In the tool editor chat** — same: roll back the conversation; optionally restore the tool config to the state at that message (or undo the chat only).
- **In the Changes panel** — open it from the agent or tool editor side panel (the **Changes** button with history icon). The list shows saved versions; versions saved by the assistant are marked with a **robot icon** (tooltip: "Changed by AI Assistant"). You can load any version from there.

## Tips

- Be specific: “Add the Summarizer tool to this agent” or “Fix why this MCP server won’t connect” works better than a vague request.
- After the assistant makes changes, review them in the editor and save when you're happy.
- For more on agents and tools, see [Agents](/help/agents) and [Tools](/help/tools).
