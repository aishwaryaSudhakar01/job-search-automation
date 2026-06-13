# Job Search Automation 

An n8n pipeline that monitors company career pages and job alert emails, filters postings with Claude AI, and sends a curated digest straight to Telegram — fully automated, end to end.

<img width="1314" height="565" alt="Workflow canvas" src="https://github.com/user-attachments/assets/c73886c0-d25a-479c-8e96-b82149f20aa7" />

---

## How it works

1. **Schedule fires** — triggers every 3 days automatically
2. **Five sources run in parallel** — Ashby, Greenhouse, and Lever APIs (free), Firecrawl for custom career pages, and Gmail for job alert emails
3. **Everything merges** — all sources combine into a single stream; email finds take priority over career page finds for the same role
4. **Deduplication** — already-seen postings are filtered out against the existing tracker
5. **Claude filters** — classifies each posting by role archetype and drops non-matches, wrong locations, and excluded role types
6. **Results land in two places** — Google Sheets gets a new row with a clickable link; Telegram gets a lettered digest or a rest-day note

---

## Tech Stack

| Layer | Technology |
|---|---|
| Orchestration | n8n |
| AI Filtering | Claude Sonnet 4.6 (Anthropic) |
| Career Page Scraping | Firecrawl |
| ATS APIs | Ashby, Greenhouse, Lever (all free) |
| Email Source | Gmail API |
| Tracker | Google Sheets |
| Notifications | Telegram Bot API |

---

## Setup

### 1. Import the workflow
Download `workflow.json` and import it into your n8n instance.

### 2. Add credentials
Add your own credentials in n8n's credential manager for:
- Anthropic API
- Google (Sheets + Gmail OAuth)
- Firecrawl
- Telegram Bot

### 3. Set up Google Sheets
Create a spreadsheet with two tabs:
- **Target** — company watchlist (columns: Company Name, Career Page URL, ATS Type)
- **Tracker** — job log (columns: Job Tracker ID, Company Name, Source, Application URL, Archetype, Job Title, Job Vacancy ID, Description, Application Status, Created At)

### 4. Activate
Toggle the workflow on. It runs on schedule from there.

---

## Notes
- Credentials are not included — add your own
- For reliable scheduling, deploy n8n to an always-on server rather than running it locally

---

## Built by

[Aishwarya Sudhakar](https://aishwaryas-portfolio.lovable.app/)
