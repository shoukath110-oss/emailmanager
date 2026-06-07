# emailmanager
Automate extracting email and attachments with passwords and extracting to excel documents like a supplier  invoice or credit card statements. 

Automated Invoice Processing Pipeline
A fully automated, end-to-end invoice ingestion and data extraction pipeline built on Claude Desktop, Cowork Pro, Gmail MCP Connector, and Claude in Chrome — designed to eliminate manual intervention in accounts payable and receivable workflows.
Overview
This pipeline automatically monitors a Gmail inbox for incoming invoices, downloads attachments, extracts structured data, and logs everything into a centralised Excel workbook — routing each invoice through the correct processing path based on configurable supplier rules.
Architecture
The pipeline runs as two independent scheduled jobs:
Schedule 1 — Download Phase: Connects to Gmail, matches emails against supplier rules, downloads invoice attachments, and places them in the Inbox folder.
Schedule 2 — Extraction Phase: Reads files from the Inbox, extracts invoice data using AI, writes results to the appropriate Excel tabs, and moves files to either the Recorded or Error folder.
This separation allows each phase to be monitored, retried, or extended independently.
Folder Structure
Code
Excel Workbook (Data Store & Config Hub)
Tab
Purpose
Instructions
Usage guide and setup notes
CONFIG
One row per supplier — single source of truth for all routing decisions
DOWNLOAD_LOG
Audit trail of all downloaded files
EXTRACTION_LOG
Record of all successfully extracted invoices
ERROR_LOG
Actionable error entries identifying the exact CONFIG column to correct
DATA
Structured invoice data output
Supported Invoice Types
Nine invoice types are supported, covering: PDF, PDF_PWD, EXCEL, EXCEL_PWD, LINK_DIRECT, LINK_PORTAL, LINK_PORTAL_PWD, IMAGE, and MIXED — each routed to the appropriate extraction method automatically.
Key Features
CONFIG-driven routing — all supplier rules in one place; no code changes needed to onboard new vendors
Body keyword matching — disambiguates multiple vendors arriving from the same sender address
Last Known Type tracking — detects when a supplier changes their invoice format
Card statement support — captures Card Number, Statement Date, Period, Due Date, Minimum Due, and Total Due
Actionable error logging — each error entry points to the exact CONFIG column requiring correction
Tech Stack
Claude Desktop + Cowork Pro
Gmail MCP Connector
Claude in Chrome
Microsoft Excel (local workbook as data store)
