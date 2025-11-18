# Aria Vista – AI Sales Call Workflows (n8n + Retell + Kommo)

This repository contains a fully working example of an **AI voice sales funnel** built on top of:

- **Retell AI** – AI voice agent that calls new leads
- **n8n** – workflow automation engine
- **Kommo CRM** – lead management and pipeline staging
- **Google Sheets** – logging batch calls
- **Google Calendar** – auto-booking meetings for interested leads

> **Brand note:**  
> “Aria Vista” is a **fictional project/brand** used only as a demo.  
> No real client data, access tokens, or credentials are included in this repo.

---

## Project Overview

The system has three main pieces:

1. **AI Call Prompt – “Jackson”**  
   A Retell AI voice agent that:
   - Calls new leads **within the next working day**
   - Only calls during **business hours (9:00–18:00, Mon–Fri)** to respect telemarketing rules
   - Warms up the lead, qualifies interest, and tries to **book a meeting**
   - Avoids calling on Sundays / public holidays (configurable by you)

2. **AI Sales Call – Create Batch Call** (`workflows/AI Sales Call - Create Batch Call.json`)  
   n8n workflow that:
   - Pulls a batch of leads from your data source (e.g. Airtable / Kommo / Sheets)
   - Sends outbound call jobs to **Retell AI** via API
   - Logs batch call IDs for later reference

3. **AI Sales Call – Call Response** (`workflows/AI Sales Call - Call Response.json`)  
   n8n workflow that:
   - Is triggered by **Retell’s webhook** once a call ends
   - Reads the call metadata and analysis from Retell
   - Uses OpenAI (via n8n nodes) to:
     - Classify intent: `interested`, `neutral`, or `not_interested`
     - Decide follow-up: `book_meeting`, `send_whatsapp`, `send_email`, or `null`
     - When appropriate, extract meeting timing from the call summary
   - Updates the lead in **Kommo**:
     - Moves lead to the correct **pipeline stage** (MQL, Meeting Booked, Follow-Up, Recycle, etc.)
     - Adds **notes** with:
       - Cleaned call summary
       - Recording URL (from Retell)
   - Optionally:
     - Creates a **Google Calendar** event for booked meetings
     - Logs / references the lead in a **Google Sheets** call log

---

## Files in This Repo

### `/workflows/AI Sales Call - Create Batch Call.json`

- n8n workflow to:
  - Fetch a list of leads to call
  - Trigger **Retell** outbound calls using:
    - `POST https://api.retellai.com/v2/create-phone-call`
  - Store the `call_id` or batch ID for each lead

**Security / placeholders:**

- `Authorization` header is set to:

  ```text
  Bearer YOUR_RETELL_API_KEY
