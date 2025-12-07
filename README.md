# n8n Automation Portfolio

This repository showcases a set of real-world automation workflows built with **n8n**.

The goal: solve concrete business problems with clean, maintainable automations that can be reused, extended, and handed off to non-technical teams.

## Workflows

### 1. Lead Intake & Notification Automation
**Folder:** `workflows/lead-intake-automation`

- Capture leads from a form / webhook
- Validate and normalize data
- Store leads into Google Sheets (or CRM)
- Notify via Slack/Telegram/Email

### 2. AI Document Processor
**Folder:** `workflows/ai-document-processor`

- Receive raw text or uploaded content
- Summarize with AI
- Extract key points and classifications
- Generate a PDF or structured JSON + optional email delivery

### 3. Data Enrichment Pipeline
**Folder:** `workflows/data-enrichment-pipeline`

- Read basic records (e.g. email + company)
- Enrich data via APIs or internal services
- Update a Sheet / DB with enriched info
- Notify / log results

---

Each workflow folder contains:

- `*.json` → n8n export
- `README.md` → explanation, inputs/outputs, business use case
- `screenshots/` → n8n canvas + example results

## Changelog

All notable changes are documented in the [CHANGELOG](./CHANGELOG.md).