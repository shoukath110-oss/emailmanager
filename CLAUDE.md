# Invoice Automation — Claude Workspace

This folder is a fully automated invoice and card statement processing workspace.
Point Claude to this folder and use one of the shorthand commands below.

---

## Quick Commands

| Say this | What it does |
|---|---|
| **extract email** | Searches Gmail for new invoices/statements per CONFIG rules and downloads them to `/Inbox/` |
| **extract invoice** | Processes regular supplier invoices in `/Inbox/` → extracts to DATA tab → moves to `/Recorded/` |
| **extract card** | Processes credit card statements in `/Inbox/` → extracts to DATACARD tab → moves to `/cardextracted/` |
| **extract all** | Runs extract invoice + extract card sequentially |
| **remove password** | Strips password protection from files in `/Inbox/` using passwords from CONFIG — saves clean files in place |
| **remove password in cardextracted** | Same — but targets `/cardextracted/` folder instead of Inbox |
| **remove password in [folder]** | Target any folder: `inbox`, `cardextracted`, `recorded`, `error` |

All three operations use **two separate Excel files**:

| File | Location | Purpose | Who writes it |
|---|---|---|---|
| `Invoice_Automation_Config.xlsx` | `C:\claude\invoices\configs\` | CONFIG rules only — Claude reads, you edit | **You only** |
| `Invoice_Logs.xlsx` | `C:\claude\invoices\` | All logs and extracted data | **Claude only** |

> ⚠️ **Never let Claude write back to `Invoice_Automation_Config.xlsx`.**
> The mount corrupts any file that Claude writes to. Keeping the config
> in a separate `C:\claude\invoices\configs\` folder that Claude only reads from
> means it always sees your latest saved version.
>
> `Invoice_Logs.xlsx` is Claude's output file — open it to view results,
> but don't edit it (Claude overwrites it each run).

---

## Known Issues & Fixes

### Stale lock file (`~$Invoice_Logs.xlsx`)
Excel sometimes leaves a `~$Invoice_Logs.xlsx` temp file behind even after closing.
This blocks Claude from writing the log. **Fix:** Claude will automatically request
permission to delete it at the start of each write. You don't need to do anything.
If it persists, manually delete `C:\claude\invoices\~$Invoice_Logs.xlsx` in Explorer.

### Mount write corruption (BadZipFile)
The Windows/Linux mount corrupts `.xlsx` files on write. Claude works around this by:
1. Always keeping a valid master copy at `/tmp/Invoice_Logs_master.xlsx`
2. Writing to `/tmp` first, then `cp` to the mount
3. If the mount copy is unreadable, Claude rebuilds from the `/tmp` master

**You never need to worry about this** — Claude handles it automatically.
If you open `Invoice_Logs.xlsx` in Excel and it looks blank or broken, close it and
tell Claude to re-copy — Claude will push the `/tmp` master back to the mount.

---

## Folder Structure

```
C:\claude\invoices\
├── configs\
│   └── Invoice_Automation_Config.xlsx   ← YOUR config (Claude reads only — never writes)
├── Invoice_Logs.xlsx                    ← Claude's output: all logs + extracted data
├── Inbox/                               ← Downloaded files waiting to be processed
├── Recorded/                            ← Successfully extracted regular invoices
├── cardextracted/                       ← Successfully extracted card statements
└── Error/                              ← Files that failed extraction (pending fix)
```

---

## File Tabs

### Invoice_Automation_Config.xlsx (your file)
| Tab | Purpose |
|---|---|
| CONFIG | One row per supplier — email rules, passwords, file patterns |

### Invoice_Logs.xlsx (Claude's output file)
| Tab | Purpose |
|---|---|
| DOWNLOAD_LOG | Log of every email download run |
| EXTRACTION_LOG | Log of every extraction attempt |
| DATACARD | Extracted credit card statement data |
| DATA | Extracted regular invoice data |
| ERROR_LOG | Errors with suggested fixes |

---

## How Supplier Routing Works

Claude uses the **last 2 characters of the Supplier/Vendor name** to route files:

- Ends in **`cc`** (case-insensitive) → **Card Statement** (DATACARD tab, `/cardextracted/`)
- Does **not** end in `cc` → **Regular Invoice** (DATA tab, `/Recorded/`)

This means you can run `extract invoice` and `extract card` safely on the same Inbox without overlap.

---

## See Also

- `system.md` — Full system architecture and field mapping reference
- `agent.md` — Exact prompts Claude runs for each co