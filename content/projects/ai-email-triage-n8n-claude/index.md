---
title: "AI Email Triage: Inbox to Action in Under 60 Seconds"
date: 2026-06-24
lastmod: 2026-07-09
tags: ["ai-automation", "n8n", "ai-agents", "rag-chatbots", "claude", "gmail", "workflow-automation"]
categories: ["projects"]
description: "A 6-node n8n workflow that uses Claude AI to classify, prioritize, summarize, and draft replies for every email — for under $8/month. Includes a live demo video."
---

![n8n workflow: Gmail Trigger → Normalize Email → Claude AI → Parse JSON → Google Sheets → Telegram Alert](thumb.png)

Every freelancer and founder I know has the same problem: the inbox is a black hole.

Hot leads sit next to recruiter spam, sit next to newsletters, sit next to actual client questions — and by the time you've sorted through it all, the hot lead has already hired someone else.

I built a workflow to fix this. Here's exactly how it works, what it costs to run, and how you can set it up yourself.

🎥 **Watch the live demo:**

{{< youtube u2z3gkxVXIM >}}

---

## The Problem

Manual email triage is deceptively expensive. If you spend just 20 minutes a day sorting and prioritizing your inbox, that's:

- 1.5 hours per week
- 6 hours per month
- **72 hours per year** — nearly two full work weeks

And that's just the time cost. The *real* cost is the hot lead you missed while you were reading a recruiter pitch.

---

## The Solution: A 6-Node n8n Workflow

I built this in n8n (self-hosted), calling Claude AI via API. The entire pipeline runs automatically every minute, costs under $0.01 per email to process, and takes under 60 seconds from email received to Telegram alert.

Here's the architecture:

```
Gmail Trigger
     ↓
Normalize Email (extract sender, subject, body)
     ↓
Claude AI (classify + prioritize + summarize + draft reply)
     ↓
Parse JSON (extract structured output)
     ↓
Google Sheets (log everything)
     ↓
Telegram Alert (instant notification)
```

### Node 1 — Gmail Trigger
Polls Gmail every minute for new emails. No webhooks needed, no complex OAuth setup beyond the initial one-time authorization.

### Node 2 — Normalize Email
A simple Set node that extracts the three fields we care about: `senderEmail`, `subject`, and `body`. This handles the inconsistency in Gmail's API response format across different email types.

### Node 3 — Claude AI (the brain)
This is where the magic happens. I send a single structured prompt to Claude Haiku (the fast, cheap model — perfect for classification tasks) and ask it to return a JSON object with exactly five fields:

```json
{
  "category": "hot_lead",
  "priority": "high",
  "language": "en",
  "summary": "David from SwiftCargo needs invoice automation, budget $1,500, wants to start in 2 weeks.",
  "suggested_reply": "Hi David, this sounds like a great fit — I've built exactly this kind of system before. Are you free for a 20-minute call this week?"
}
```

The prompt enforces strict JSON output (no markdown fences, no preamble) so the next node can parse it reliably. Categories are: `hot_lead`, `question`, `recruiter`, `spam`, `other`.

**Cost:** Claude Haiku processes a typical email for under $0.001. Even at 100 emails/day, that's under $3/month.

### Node 4 — Parse JSON
A Code node that strips any accidental markdown formatting from Claude's response and parses the JSON safely. It also merges the AI output with the original email metadata so the next nodes have everything they need.

### Node 5 — Google Sheets
Appends one row per email with all eight fields: `receivedAt`, `senderEmail`, `subject`, `category`, `priority`, `language`, `summary`, `suggested_reply`. The sheet becomes a clean, searchable audit trail of every email processed.

### Node 6 — Telegram Alert
Fires a formatted message for every email. On my phone, I can see at a glance:

```
🚨 hot_lead · priority: high · en
From: david@swiftcargo.com
Subject: Need help automating invoice processing

Summary: Logistics company, manual invoice entry,
budget $1,500, wants to start in 2 weeks.

✍️ Draft reply:
Hi David, this sounds like a great fit...
```

I can reply directly from Telegram in under 30 seconds — before the lead has even refreshed their inbox.

---

## Live Demo: Two Real Test Emails

### Test 1 — Catching a Hot Lead in Real Time

I sent this email to my own Gmail:

> **From:** david@swiftcargo.com
> **Subject:** Need help automating our invoice processing — budget ready
> **Body:** We're drowning in supplier invoices entered by hand. Budget ~$1,500. Can we start in two weeks?

Within 60 seconds, Telegram pinged me:
- Category: `hot_lead`
- Priority: `high`
- Draft reply ready to send

### Test 2 — Filtering Spam Automatically

I sent a classic "you've won $5,000,000" spam email. The workflow correctly returned:
- Category: `spam`
- Priority: `low`
- No alert noise

The AI didn't just keyword-match — it understood context. That's the difference between a rule-based filter and an LLM.

You can watch both tests run end-to-end in the demo video above.

---

## The Stack & What It Costs

| Component | Tool | Monthly Cost |
|---|---|---|
| Workflow automation | n8n (self-hosted) | ~$5 VPS |
| AI classification | Claude Haiku (Anthropic) | < $3 at 100 emails/day |
| Email source | Gmail API | Free |
| Data logging | Google Sheets API | Free |
| Notifications | Telegram Bot API | Free |
| **Total** | | **< $8/month** |

---

## What This Actually Saves

For a freelancer handling 30–50 emails/day:

- **Time saved:** 15–20 minutes of manual sorting per day
- **Leads captured:** Zero missed hot leads during busy periods
- **Response speed:** From hours to under 2 minutes for high-priority emails

For a small business team sharing one inbox, the numbers multiply.

---

## Want This for Your Inbox?

This is the kind of automation I build for clients on Upwork — systems that run quietly in the background and surface what matters, so you can focus on the work that actually grows your business.

If your team is spending time on repetitive inbox work (or worse, missing important messages), [message me on Upwork](https://www.upwork.com/freelancers/etuannv) and I'll tell you exactly how I'd automate it.

---

## Want to Build It Yourself?

The full workflow is open source on GitHub — **[n8n-ai-email-triage](https://github.com/etuannv/n8n-ai-email-triage)**. Import the JSON and you'll need:

- n8n (self-hosted or cloud)
- Anthropic API key (~$5 credit to start)
- Gmail account + Google Cloud project (free)
- Telegram bot (free, takes 2 minutes via @BotFather)
- Google Sheet

Setup time: about 2 hours for someone comfortable with APIs.

---

*Tuan Nguyen is a Top Rated Plus automation developer on Upwork with 10+ years in Python and data engineering. He builds AI automation systems, n8n workflows, and RAG chatbots for clients worldwide.*

*→ [Upwork profile](https://www.upwork.com/freelancers/etuannv) · [Freelancer profile](https://www.freelancer.com/u/etuannv)*

---
**See also:**
- [AI Lead Capture — from form submission to CRM in 30 seconds](https://etuannv.com/posts/n8n-ai-lead-capture-airtable/) — the same qualify-and-route pattern applied to contact-form leads
- [Prompt Engineering in 5 Levels](https://etuannv.com/posts/prompt-engineering-5-levels/) — the prompt techniques behind this workflow's Claude classification
- [Claude Code, Efficiently](https://etuannv.com/posts/claude-code-efficiently/) — how I build and ship automations like this one faster
