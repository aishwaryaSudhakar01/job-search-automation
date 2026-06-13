# Job Search Automation

An n8n workflow that automatically discovers job postings, filters them with
Claude AI, logs matches to Google Sheets, and sends a Telegram digest.

## What it does
- Pulls live job postings from Ashby, Greenhouse, and Lever APIs (free) and
  custom career pages (Firecrawl)
- Reads job-alert emails via the Gmail API as a second source
- Deduplicates against an existing tracker, with email sources taking priority
- Uses Claude to classify each posting by role archetype and filter out non-matches
- Writes new matches to a Google Sheet with clickable links
- Sends a Telegram message listing the day's finds (or a rest-day note)

## Architecture
Schedule Trigger -> (Company watchlist + Gmail) -> ATS/Firecrawl/Email parsing ->
Merge -> Dedupe -> Claude filter -> Google Sheets + Telegram

## Tools
n8n, Claude (Anthropic API), Firecrawl, Google Sheets API, Gmail API, Telegram Bot API

## Setup
1. Import workflow.json into n8n
2. Add your own credentials for Anthropic, Google, Firecrawl, and Telegram
3. Populate a Google Sheet with a "Target" tab (company watchlist) and a "Tracker" tab
4. Activate the workflow

## Note
Credentials are not included. Add your own in n8n's credential manager.
