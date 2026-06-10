# Batch run

**Batch run** runs the agent **once per item** from a list you define—files in a folder, rows in a CSV, values in a list, or a number range. You get a **queue with progress**, can **pause**, **retry** failed items, and optionally **export** all results to one table (CSV, Excel, or JSON Lines).

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
   - **Excel / CSV** — **one row per item** (worksheet for Excel, delimiter for CSV, header, and row range under **Parsing options**).
   - **Value list** — lines you enter in settings (or override at launch).
   - **Number range** — integers from start to end with a step.
3. **For each run** — map each item to the agent’s input fields (e.g. a CSV column → a text field). The UI can auto-fill common mappings when you enable batch or change the source.
4. **Execution settings** (optional) — **Concurrency** (how many items run in parallel), **When a run fails**, **Retry** settings.
5. **Export results** (optional) — turn on **Save a summary file when the batch finishes** and optionally set **Save to** for auto-export when the batch completes. You can leave **Save to** empty and use **Export results…** after the run (see [Export results](#export-results)).
6. **After batch** (optional) — run another agent or steps when the whole batch completes.

Save the agent (**Apply** in **Batch settings**, then save the agent). For triggers, **Activate** the agent so scheduled or file-driven batches can start.

## Starting a batch

- **Run batch** — manual start from the toolbar; override paths or lists for this run only in **Run settings** (**File for this run**, folder path, value list, number range, or summary export path).
- **Schedule / Interval** — each firing starts **one** batch from **Batch settings** (not a single normal run). The trigger subtitle shows whether batch is ready. For **CSV** source, set a **Default file (optional)** in Batch settings (or override in **Run batch**); scheduled runs do not use a file-trigger path.
- **File system** — see [File trigger and batch](#file-trigger-and-batch) below.

Until source and mapping are complete, triggers may fall back to **one normal run per event**; finish **Batch settings** first. Check the hint under the file trigger in the editor.

## File trigger and batch

When **Group files** is **off** and **Batch run** is **on**, each matching file event starts a batch (if settings are ready). What counts as an **item** depends on the **Source** type:

| Settings | What one file event does |
|----------|---------------------------|
| Batch **off** | **One** normal agent run per file (`Input File` from the trigger) |
| Batch **on**, source **Excel / CSV** | **One batch** on the **file that triggered** the event; items = **rows in that file** (one agent run per row, not one run for the whole file) |
| Batch **on**, source **Folder** | **One batch** that rescans the **folder path from Batch settings** (all matching files there—not only the file from the event) |
| **Group files** **on** | **One** agent run with several files in **Input File**; batch run is **not** used |

**Excel / CSV and the trigger:** the watched path on the **file trigger** (e.g. `*.xlsx` or `*.csv` in a folder) decides **when** to start. **Parsing options** and **For each run** mapping in Batch settings decide **how** that file is read. **Default file (optional)** in Batch settings is **not** required for file-trigger batches—the triggering file path is used automatically. Use **Default file** for **Run batch** and **Schedule / Interval** runs.

## During and after a run

- **Pause** — stop scheduling new items; items already running finish.
- **Cancel** — stop pending and in-flight work for this batch.
- **Retry (N)** — re-run failed items.
- **Continue (N)** — run items that did not complete successfully (e.g. skipped or cancelled).
- **Results** — each item has its own execution; open item rows to see logs and output like a normal run.

Unnot always keeps batch data in an internal run folder (`results.jsonl` per batch). That is separate from the optional **combined export** file you choose in settings.

## Export results

| | **Combined export** (CSV / Excel / JSON Lines) | **Run folder** (automatic) |
|--|----------------------------------------|----------------------------|
| **What** | One row per item (status + outputs) for you | Technical storage for the app |
| **When** | Auto on finish **if** **Save to** is set; or **Export results…** after the batch ends | Every batch run |

**Tips:**

- You can **start a batch without** a summary export path; use **Export results…** on the items toolbar after the batch finishes.
- **Save to (optional)** in Batch settings and **Browse** there use **Save As**—the file is created on export, not chosen from existing files.
- **Add timestamp to file name** (on by default) — each run writes a new file (e.g. `batch-my-agent-2026-06-03_14-30-45.csv`); turn off to overwrite the same path every run.
- **Export results…** opens **Save As** again (re-export is allowed); format follows the file extension you pick (`.csv`, `.xlsx`, or `.jsonl`).

## Subagents

If this agent is attached as a **subagent** and **batch run** is enabled here, a parent agent can start a **batch** on it (one parent tool call, many child runs). Configure source and mapping on the **subagent**; the parent only passes batch overrides (paths, list, range) allowed by the tool. For chaining after a single parent run, use **post-execution** instead ([Agents](/help/agents#pre--and-post-execution)).

## See also

- [Agents](/help/agents) — triggers, activate, normal **Run**
- [Local files and folders](/help/local-files) — **File path** / **Folder path** fields and file triggers
- [Troubleshooting](/help/troubleshooting#batch-run) — batch not starting from triggers, export issues
