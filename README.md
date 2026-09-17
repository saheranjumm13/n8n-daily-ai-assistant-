# Daily AI Assistant (n8n)

An n8n workflow that uses an AI Agent to understand natural-language task requests, extract structured details (task, category, priority, deadline), and save them to Google Sheets — the foundation of a personal daily productivity assistant.

## What it does

Instead of manually opening a spreadsheet and typing out a task, you tell the agent what you need in plain language, and it figures out the rest.

**Example input:**
```
Add a task to finish my Power BI dashboard today. Priority high.
```

**What happens:**
1. The AI Agent reads and understands the message.
2. It identifies the task, category, priority, and deadline (if mentioned).
3. It calls the Google Sheets tool to save the task.
4. It confirms back what was saved.

## Current architecture

```
Manual Trigger
      ↓
   AI Agent  ──→  Google Gemini Chat Model (reasoning)
      │       ──→  Simple Memory (conversation context)
      │       ──→  Append Row in Google Sheets (save new tasks)
      │       ──→  Get Row(s) in Google Sheets (read existing tasks)
      ↓
   Response
```

## Components

| Node | Role |
|---|---|
| **Manual Trigger** | Temporary stand-in for a real input source (Telegram, chat, etc.) — used for testing |
| **AI Agent** | Decision-maker: interprets the request and decides which tool to call |
| **Google Gemini Chat Model** | The language model powering the Agent's reasoning |
| **Simple Memory** | Lets the Agent recall earlier messages in the same session |
| **Append Row (Google Sheets)** | Saves new tasks to the spreadsheet |
| **Get Row(s) (Google Sheets)** | Retrieves existing tasks (e.g. "what's pending?") |

## Google Sheet structure

The connected sheet (`AI Assistant Tasks`) uses these columns:

| Task | Category | Priority | Deadline | Status |
|---|---|---|---|---|
| Study SQL | Study | High | Today | Pending |
| Finish Power BI dashboard | Project | High | Today | Pending |

**Categories:** Study, Career, Project, Personal, Health
**Statuses:** Pending, In Progress, Completed

## System message (AI Agent instructions)

```
You are my personal daily productivity assistant.

When I give you a new task:
1. Understand the task.
2. Identify the category.
3. Identify priority.
4. Identify deadline if provided.
5. Save the task in Google Sheets using the available tool.
6. Set the status to Pending.
7. Confirm that the task was saved.

Never invent a deadline that wasn't provided.
```

## Setup

1. Import `workflow.json` into your n8n instance (Workflows → Import from File).
2. Connect your own credentials:
   - Google Gemini (or another supported chat model)
   - Google Sheets (OAuth)
3. Create a Google Sheet matching the column structure above, and point both Sheets nodes to it.
4. Click **Execute Workflow** and test with a sample task.

> **Note:** No API keys or credentials are included in this repository. You must connect your own.

## Roadmap

- [x] Phase 1 — Basic AI Agent responding to messages
- [x] Phase 2 — Save tasks to Google Sheets (Append Row)
- [x] Phase 3 — Read tasks back from Google Sheets (Get Row)
- [x] Phase 4 — Add conversational memory
- [ ] Phase 5 — Replace Manual Trigger with Telegram Trigger for mobile use
- [ ] Phase 6 — Update Row tool (mark tasks as Completed)
- [ ] Phase 7 — Scheduled Daily Planner workflow (runs every morning, sends a plan via Telegram)
- [ ] Phase 8 — Reminder Agent (checks upcoming deadlines and notifies proactively)
- [ ] Phase 9 — Career Assistant mode (interview prep, practice questions, resume help)

## Tech stack

- [n8n](https://n8n.io/) — workflow automation
- Google Gemini — language model
- Google Sheets — task storage
- (Planned) Telegram Bot API — mobile interface

## License

MIT
