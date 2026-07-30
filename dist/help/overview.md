# What is Unnot

**Unnot** is a desktop app that helps you automate everyday tasks with AI. You describe what you want done and stay in charge of how it runs—no coding background required.

You might start with something familiar: work that repeats on a schedule, a folder you want Unnot to notice when something changes, or a task that needs both a clear decision and a real-world next step. The sections below introduce Unnot piece by piece; follow the links at the end for step-by-step guides.

## Main areas

In the **sidebar**:

- **Chat** — one-off questions, files, and tools (no agent required).
- **Agents** / **Tools** — your workflows and MCP servers.
- **LLM Accounts**, **Settings**, **Help** — keys, app preferences, and this documentation.

**Tool Gallery** opens from **Create** (or Tools) when you want to install a new MCP package.

## How it works

**Agents** combine **instructions** for the model, optional **tools**, **input and output fields**, and **triggers** that decide when a run starts—everything for one workflow, in one place. You refine each agent in the editor, run it from the same screen, and come back to results and history whenever you need them.

**Tools** are how agents move from answers to actions—files, search, APIs, and whatever else an **MCP** (Model Context Protocol) server exposes as “do this for me” steps. A server may run **locally** on your machine or **remotely** over the network. Attach the tools you trust to the agents that need them. **Tool Gallery** lets you discover and install new tools from registries **without leaving the app** ([Tool Gallery](/help/gallery)).

**Chat** (sidebar) is a one-off assistant: ask, attach a file, or use tools without opening an agent. **AI Assistant** in the **Agent** or **Tool** editor helps configure that open item in plain language. Same model settings (**Settings → AI Assistant**). Details: [AI Assistant](/help/ai-assistant).

**LLM Accounts** are where you add **providers** and **API keys**—for example OpenAI, Anthropic, OpenRouter, and others—so each agent can run with the account and model you choose.

**Settings** rounds up app-wide preferences—the folder Unnot uses on disk, limits for agents and MCP connections, backup and logging, proxy when your network needs it, and the model for Chat / AI Assistant (**Settings → AI Assistant**). That model is separate from the model each agent uses when it runs.

**Your data** lives in **one folder you choose**: agents, tools, and backups together. **API keys and other sensitive credentials** are stored in your **operating system’s secure credential store**, not as readable text inside your data folder. Unnot runs on your machine; LLM requests and remote tools still use the network when needed—that’s how cloud models and integrations work. What you keep is **clarity and control**: what is stored where, what is connected, and how your agents are set up to run.

## Where to go next

- [Getting Started](/help/getting-started) — first agent and basic setup.
- [Agents](/help/agents) — triggers, status, and how runs work.
- [Batch run](/help/batch-run) — process many files, CSV rows, or list values with progress and export.
- [Local files and folders](/help/local-files) — file and folder paths per run, agent resources, and MCP file tools.
- [Tools](/help/tools) — transports, connection, troubleshooting.
- [Tool Gallery](/help/gallery) — discover and install tools safely.
- [LLM Accounts](/help/llm-accounts) — providers and models.
- [Settings](/help/settings) — storage, limits, MCP, backup, logging, assistant model.
- [AI Assistant](/help/ai-assistant) — Chat, and assistants in the Agent / Tool editors.
- [Automation (CLI & API)](/help/automation) — run agents and query status from scripts or the REST API while the app is running.
- [Contact](/help/contact) — how to reach us and what to include.

**References:** [Model Context Protocol](https://modelcontextprotocol.io) · [MCP on GitHub](https://github.com/modelcontextprotocol) · [Tools schema (advanced)](/help/schemas/unnot-tools-full.schema.json) · [Agents schema (advanced)](/help/schemas/unnot-agents-full.schema.json)
