# Batch run

**Batch run** runs the agent **once per item** from a list you define—files in a folder, rows in a CSV, values in a list, or a number range. You get a **queue with progress**, can **pause**, **retry** failed items, and optionally **export** all results to one table (CSV or JSON Lines).

Use it when you need to process **many similar items** the same way, not when you want **one** run that sees several files at once (see [Group files vs batch run](#group-files-vs-batch-run) below).

## Group files vs batch run

| | **Group files** (file trigger) | **Batch run** (agent settings) |
|--|-------------------------------|--------------------------------|
| **Where** | **Triggers** → file trigger → **Group files before run** | Side panel → **Batch** → **Batch settings** |
| **Runs** | **One** agent run per batch of events | **One** agent run **per item** |
| **Input** | Several files in **Input File** at once | **One** file/path/value per run (via item mapping) |
| **Good for** | Compare or handle a **set of files in one prompt** | **Mass processing** with progress and export |

Do not enable both for the same workflow unless you understand the difference.

## Where in the UI

1. Open the agent in the editor.
2. In the **side panel**, click **Batch** → **Batch settings** opens.
3. Turn on **Enable batch run**, then configure **Source**, **For each run**, and optional sections.
4. Start work with **Run batch** in the editor toolbar (next to **Run** / **Run agent** when batch is enabled), or let a **Schedule**, **Interval**, or **File system** trigger start a batch when the agent is **Activated**.

While a batch is running, open the **Run batch** panel to see items, status, and actions (**Pause**, **Resume**, **Cancel**, **Retry**, **Continue**, **Export results…**).

## Quick setup

1. **Enable batch run** in **Batch settings**.
2. **Source** — choose what each item is:
   - **Folder** — files matching include/exclude patterns (and optional recursion).
   - **CSV file** — one row per item (with header row and column mapping as needed).
   - **Value list** — lines you enter in settings (or override at launch).
   - **Number range** — integers from start to end with a step.
3. **For each run** — map each item to the agent’s input fields (e.g. file path → **File path**). The UI can auto-fill common mappings when you enable batch or change the source.
4. **Execution settings** (optional) — **Concurrency** (how many items run in parallel), **When a run fails**, **Retry** settings.
5. **Export results** (optional) — **Auto-export combined file when a path is set** writes a summary file when the batch **finishes**, only if a path is set (default path is optional). You can also export after the run with **Export results…** (see below).
6. **After batch** (optional) — run another agent or steps when the whole batch completes.

Save the agent (**Apply** in **Batch settings**, then save the agent). For triggers, **Activate** the agent so scheduled or file-driven batches can start.

## Starting a batch

- **Run batch** — manual start from the toolbar; you can override folder path, CSV path, value list, number range, or export path for this run only (**Run settings** in the panel).
- **Schedule / Interval** — each firing starts **one** batch using **Batch settings** (not a single normal run). The trigger subtitle shows whether batch is ready.
- **File system** — with batch enabled and **Group files** off, each file event can start a batch:
  - **CSV** source uses the **file that triggered** the event as the CSV path.
  - **Folder** source rescans the **folder path from Batch settings** (all matching files), not only the triggering file.

Until source and mapping are complete, triggers may fall back to **one normal run per event**; finish **Batch settings** first.

## During and after a run

- **Pause** — stop scheduling new items; items already running finish.
- **Cancel** — stop pending and in-flight work for this batch.
- **Retry (N)** — re-run failed items.
- **Continue (N)** — run items that did not complete successfully (e.g. skipped or cancelled).
- **Results** — each item has its own execution; open item rows to see logs and output like a normal run.

Unnot always keeps batch data in an internal run folder (`results.jsonl` per batch). That is separate from the optional **combined export** file you choose in settings.

## Export results

| | **Combined export** (CSV / JSON Lines) | **Run folder** (automatic) |
|--|----------------------------------------|----------------------------|
| **What** | One row per item (status + outputs) for you | Technical storage for the app |
| **When** | Auto on finish **if** a path is set; or **Export results…** after the batch ends | Every batch run |

**Tips:**

- You can **start a batch without an export path**; set a path later and use **Export results…** on the items toolbar.
- **Unique file per run** adds a timestamp to the filename; **Fixed path (overwrite)** replaces the same file.
- **Export results…** lets you pick format and path again (re-export is allowed).

## Subagents

If this agent is attached as a **subagent** and **batch run** is enabled here, a parent agent can start a **batch** on it (one parent tool call, many child runs). Configure source and mapping on the **subagent**; the parent only passes batch overrides (paths, list, range) allowed by the tool. For chaining after a single parent run, use **post-execution** instead ([Agents](/help/agents#pre--and-post-execution)).

## See also

- [Agents](/help/agents) — triggers, activate, normal **Run**
- [Local files and folders](/help/local-files) — **File path** / **Folder path** fields and file triggers
- [Troubleshooting](/help/troubleshooting#batch-run) — batch not starting from triggers, export issues
