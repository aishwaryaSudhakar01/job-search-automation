```
# Job Search Automation

An automated job discovery pipeline built with n8n that monitors 95+ company career pages and email sources, filters postings using Claude AI, and delivers a curated digest via Telegram.

<img width="1320" height="576" alt="image" src="https://github.com/user-attachments/assets/2e8f0972-58bd-47d9-b256-160f6b97ba94" />

## Problem
Manually checking dozens of company career pages and job alert emails is slow and inconsistent. Relevant postings get missed. This pipeline automates the entire discovery and filtering process.

## How it works

**Discovery (5 parallel sources)**
- **Ashby, Greenhouse, Lever APIs** — free structured endpoints that return all current postings in one call, no scraping needed
- **Firecrawl** — scrapes custom career pages for companies not on a standard ATS
- **Gmail** — reads job alert emails directly via the Gmail API, extracting structured fields from email HTML

**Filtering**
- All 5 sources merge into a single stream
- Deduplication runs against the existing tracker, with email sources taking priority over career page finds for the same role
- Claude (claude-sonnet-4-6) classifies each posting by role archetype and filters out non-matches, wrong locations, excluded role types, and anything that isn't a real internship posting

**Output**
- Matched postings are appended to a Google Sheet tracker with clickable job links
- A Telegram message delivers the day's finds as a lettered list, or a rest-day note if nothing new was found

## Architecture

```
Schedule Trigger
├── Read Target Companies (Google Sheets, 95 companies)
│   └── Switch (by ATS type)
│       ├── Ashby      → HTTP Request → Parse Ashby
│       ├── Greenhouse → HTTP Request → Parse Greenhouse
│       ├── Lever      → HTTP Request → Parse Lever
│       └── Custom     → Firecrawl   → Parse Firecrawl
└── Email Source (Gmail) → Parse Email
         ↓
       Merge (5 inputs)
         ↓
  Load All Tracker URLs
         ↓
       Dedupe
         ↓
  Claude (Basic LLM Chain + Structured Output Parser)
         ↓
    Final Parsing
    ├── Append Jobs to Tracker (Google Sheets)
    └── Aggregate → Telegram
```

## Tools
| Tool | Purpose |
|---|---|
| n8n | Workflow orchestration |
| Claude (Anthropic) | AI filtering and archetype classification |
| Firecrawl | Scraping custom career pages |
| Google Sheets API | Company watchlist + job tracker |
| Gmail API | Reading job alert emails |
| Telegram Bot API | Digest delivery |

## Setup
1. Import `workflow.json` into your n8n instance
2. Add credentials for Anthropic, Google (Sheets + Gmail), Firecrawl, and Telegram
3. Create a Google Sheet with two tabs: **Target** (company watchlist with columns: Company Name, Career Page URL, ATS Type) and **Tracker** (job log)
4. Populate the Target tab with your company list
5. Activate the workflow

## Notes
- Credentials are not included — add your own in n8n's credential manager
- The Firecrawl node is limited to 1 company in the repo for testing; remove the Limit node before deploying
- Runs on a schedule (every 3 days by default); for reliable scheduling, deploy to an always-on server
```
