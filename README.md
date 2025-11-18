# Aria Vista – AI Sales Call Workflows  
### (n8n + Retell + Kommo + Google Sheets + Google Calendar)

This repository contains a complete example of an **AI-powered voice sales system** that:

- Automatically calls new leads using **Retell AI**
- Processes the call results through **n8n**
- Updates your **Kommo CRM** pipeline
- Logs call metadata in **Google Sheets**
- Books meetings automatically in **Google Calendar**

This is the same architecture used by modern AI-first sales teams, now provided as a **safe, anonymised template** you can import, customise, and deploy.

---

## ⚠️ About the Demo Brand “Aria Vista”

**Aria Vista is a fictional demo project.**  
There is **no real client**, and no sensitive information is included.

All tokens, credentials, domains, and IDs in these workflow files have been:

- **Removed**
- **Replaced with placeholders**
- **Safe for public sharing**

You must reconnect your own credentials after importing the workflows into n8n.

---

# 📌 Overview

This AI sales system is made of **three main components**:

---

## 1. 🎙 AI Call Prompt – “Jackson”

A Retell AI voice agent designed to:

- Call new leads **within one working day**
- Only call during **Mon–Fri, 09:00–18:00** (configurable)
- Respect telemarketing rules (avoids Sundays & holidays)
- Warm up and qualify leads
- Attempt to **book a meeting** with a human agent

The voice prompt for Jackson is included in this repo (`prompts/jackson-call-prompt.md`).

---

## 2. 🧩 AI Sales Call – Create Batch Call  
**File:** `workflows/AI Sales Call - Create Batch Call.json`

This n8n workflow:

- Fetches a batch of leads from your chosen source  
  _(Airtable, Kommo, Google Sheets, or any API source — fully changeable by you)_
- Creates outbound call jobs using Retell’s API: POST https://api.retellai.com/v2/create-phone-call
- Logs each `call_id` so the system can track responses

### Security Notes

In this public version:

- API keys are replaced with `YOUR_RETELL_API_KEY`
- Caller ID is set to a dummy number
- Airtable/Kommo/Sheet IDs are replaced with placeholders
- All credentials were removed from the JSON (you reconnect them inside n8n)

---

## 3. ☎️ AI Sales Call – Call Response  
**File:** `workflows/AI Sales Call - Call Response.json`

This workflow is triggered **after every completed call**, via Retell’s webhook.

### It performs the following automation:

#### 1. Retrieve call metadata  
From the Retell webhook payload.

#### 2. Analyze the call using AI  
OpenAI is used to classify:

- `intent`:  
- `interested`  
- `neutral`  
- `not_interested`

- `follow_up_method`:  
- `book_meeting`  
- `send_whatsapp`  
- `send_email`  
- `null` (if no action needed)

- If a call mentions a time (“tomorrow at 3pm”), extract **meeting details**

#### 3. Update Kommo pipeline  
Based on the AI’s classification, the lead is moved to appropriate stages such as:

- **Meeting Booked**
- **Follow-Up**
- **MQL**
- **Recycle Lead**

#### 4. Write notes into Kommo  
Each call logs:

- Cleaned call summary  
- Recording URL  
- Meeting details (if any)

#### 5. Book Google Calendar events  
If the lead wants a meeting:

- The workflow extracts the time using AI
- Creates an event in Google Calendar
- Adds the lead as an attendee
- Inserts meeting notes + call summary

#### 6. Log call in Google Sheets  
The workflow reads and writes to a **Call Log sheet**, identified by: SPREADSHEET_ID

---

# 🛠 Setup Instructions

## 1. Install & open n8n  
Cloud or self-hosted both work.

## 2. Import the workflows  
In n8n:

- **Workflows → Import → From File**
- Upload both JSON files from `/workflows`

## 3. Reconnect your credentials  
Inside n8n, reconnect:

- Kommo OAuth
- Retell API (HTTP Request node)
- Google Sheets OAuth
- Google Calendar OAuth
- OpenAI API

## 4. Replace placeholders  
Search for these:

- `YOUR_RETELL_API_KEY`
- `SPREADSHEET_ID`
- `example-account.kommo.com`
- `calendar@example.com`

Replace with your actual data.

## 5. Configure Retell webhook  
Paste the **Production Webhook URL** from the “Call Response” workflow into your Retell dashboard.

## 6. Customize the voice prompt  
Edit `prompts/jackson-call-prompt.md` to fit your brand/system.

---

# 🧪 Testing

1. Add a dummy lead into your CRM or source sheet  
2. Trigger the **Create Batch Call** workflow  
3. Wait for Retell to complete the call  
4. Observe how:
   - Notes get added
   - Pipeline status changes
   - Google Calendar events get created (if relevant)
   - Google Sheet logs get updated

---

# 💡 Why This Repo Exists

To provide a **transparent, safe, open-source example** of:

- AI voice sales funnels  
- CRM-integrated automations  
- Multi-step LLM-driven decision logic  
- Real-world AI → CRM → Calendar workflows  

Without exposing any:
- Client information  
- Tokens  
- API credentials  
- Personal workspace URLs  

Use it as a template for:
- Real estate  
- SaaS sales  
- Agencies  
- Service businesses  
- High-volume lead handling  

---

# 📄 License

This project is released under the **MIT License**, allowing commercial and private use.

---
