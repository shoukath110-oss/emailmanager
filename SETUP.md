# Invoice Automation — Setup Guide

This package gives you a fully automated invoice and credit card statement workflow using Claude (Cowork mode).
Once set up, you run everything by typing simple commands like `extract email`, `extract card`, or `remove password`.

---

## What You Need

1. **Claude account** — Pro or Max plan (for Cowork mode)
2. **Claude desktop app** — [claude.ai/download](https://claude.ai/download)
3. **Claude in Chrome extension** — install from Chrome Web Store (needed to download email attachments)
4. **Gmail account** — connected to Cowork via the Gmail MCP connector

---

## One-Time Setup (10 minutes)

### Step 1 — Copy this folder to your PC
Place the entire folder **anywhere** on your PC — the path does not matter:
```
D:\jamal\project\invoices\     ← works
C:\MyCompany\invoices\         ← works
E:\Documents\Finance\          ← works
```
Claude reads everything through the Cowork mount, not from a fixed Windows path.
Keep the folder structure intact:
```
invoices\
├── configs\
│   └── Invoice_Automation_Config.xlsx   ← edit this with your suppliers
├── Invoice_Logs.xlsx                    ← Claude writes here — don't edit
├── Inbox\
├── Recorded\
├── cardextracted\
└── Error\
```

### Step 2 — Fill in your Config
Open `configs\Invoice_Automation_Config.xlsx` and replace the sample rows with your real suppliers.

Key columns to fill per supplier:

| Column | What to enter |
|---|---|
| Company Name | Your company or your name |
| Supplier / Vendor | Bank/supplier name. **End with `cc`** (e.g. `RAK CC`) for credit card statements |
| From Email | The sender email address for that invoice/statement |
| Subject Keyword | Part of the email subject line |
| Date From / Date To | The date range to search (DD/MM/YYYY) |
| Invoice Type | `PDF_PWD` for password-protected PDFs, `PDF` for plain |
| File Password | The password to open the file |
| Download Folder Path | ⚠️ **Must match your actual Inbox folder path**, e.g. `D:\jamal\project\invoices\Inbox` — this is the only path-specific setting |
| File Prefix Pattern | Keep as `{Company}_{Supplier}_{Month}` |
| Active (Y/N) | `Y` to include this row in searches |
| Remove Password (Y/N) | `Y` to auto-strip password after download |

> ⚠️ **Never rename or move the config file after setup** — Claude reads it by path.
> Also never let Claude write to this file. Claude only reads it.

### Step 3 — Connect Gmail in Cowork
In the Claude desktop app:
1. Open Cowork mode
2. Go to Plugins / Connectors → Add → Gmail
3. Authorise with your Google account

### Step 4 — Connect your invoices folder in Cowork
In Claude (Cowork), click **Connect folder** and select your `invoices\` folder.

### Step 5 — Test it
Type: `extract email`

Claude will read your config, search Gmail, and download matching files to your `Inbox\` folder.

---

## Daily Usage Commands

| Type this | What happens |
|---|---|
| `extract email` | Searches Gmail and downloads new invoices/statements to Inbox |
| `extract card` | Processes credit card statements from Inbox → extracts data → moves to cardextracted |
| `extract invoice` | Processes regular invoices from Inbox → extracts data → moves to Recorded |
| `extract all` | Runs extract invoice + extract card |
| `remove password` | Strips PDF passwords from files in Inbox |
| `remove password in cardextracted` | Strips passwords from files in cardextracted folder |
| `remove password in [folder]` | Target any folder: inbox, cardextracted, recorded, error |

---

## How the Config Routing Works

Claude uses the **last 2 characters of the Supplier/Vendor name** to decide what to do with each file:

- Ends in `cc` (e.g. `RAK CC`, `EI CC`) → **Credit card statement** → DATACARD tab + `/cardextracted/`
- Anything else → **Regular invoice** → DATA tab + `/Recorded/`

---

## Password Formats (common UAE banks)

| Bank | Password format | Example |
|---|---|---|
| Emirates NBD / Emirates Islamic | First 4 chars of name (caps) + DDMM of birth | `SHOU1401` |
| RAKBANK | Last 4 digits of card + DDMM of birth | `70081401` |
| ADCB | Customer ID (8 digits) | `10357580` |
| HSBC | DDMMYY + last 6 digits of card | check your statement email |

---

## Viewing Results

Open `Invoice_Logs.xlsx` (in the root of your invoices folder) to see:
- **DOWNLOAD_LOG** — every email downloaded
- **EXTRACTION_LOG** — every file processed
- **DATACARD** — all credit card statement data extracted
- **DATA** — all invoice data extracted
- **ERROR_LOG** — any files that failed, with suggested fixes

---

## Troubleshooting

**"No emails found"** → Check From Email and Subject Keyword in config match exactly what Gmail receives. Also check Date From / Date To covers the right period.

**"Password error"** → Double-check the password in config col J. Common mistake: RAKBANK password uses last 4 digits of card, not full number.

**Config n