# IT Automation Suite

A self-built IT automation system that handles Slack ticket responses and employee onboarding across multiple client companies — automatically, without manual intervention.

Built by a solo IT specialist managing 4 companies simultaneously.

---

## The Problem

As the sole IT person managing 4 separate companies, I was:
- Manually responding to every Slack IT ticket across 4 workspaces
- Manually creating Google accounts for every new hire
- Manually sending welcome emails with login credentials
- Constantly context-switching between companies

This system automates all of it.

---

## What It Does

### 1. Auto-Reply to IT Tickets
When someone submits an IT support ticket in any of the 4 company Slack channels, a bot automatically replies in the ticket thread — instantly, even at 2am.

The reply is based on ticket priority:
- **Critical/Emergency** (system down, can't work) → urgent response
- **High Priority** (single user blocked) → high priority acknowledgment
- **Normal/Low** → standard acknowledgment

No more leaving people waiting for a response just to say "got it."

### 2. New Hire Onboarding — Automated
When HR posts a new hire in the onboarding Slack channel, the system:
1. Detects the new hire request automatically
2. Creates their Google Workspace account
3. Assigns the correct license
4. Adds their personal email as recovery
5. Drafts the welcome email with their credentials
6. Schedules the email to send 1 week before their start date (or immediately if under 7 days away)
7. Sends me a private notification in a dedicated channel

What used to take 20-30 minutes of manual work now happens automatically.

---

## Architecture

```
Slack (4 company workspaces)
        ↓  (new message event)
Slack Events API
        ↓  (HTTP POST)
ngrok tunnel  →  Next.js webhook endpoint (/api/webhooks/slack)
        ↓
   Two handlers:

   IT Ticket?          New Hire?
       ↓                   ↓
   Analyze priority    Notify me in
   Auto-reply in       #onboarding-ops
   thread as Jeff-Bot  (private channel)
                           ↓
                       GAM (Google Admin Manager)
                       Creates Google account
                           ↓
                       Gmail API
                       Drafts welcome email
                           ↓
                       Scheduled Task
                       Sends email on start date - 7 days
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **Next.js** | Webhook server that receives Slack events |
| **Slack Events API** | Real-time event delivery when tickets/hires are posted |
| **Jeff-Bot (Slack App)** | Posts auto-replies as a bot, not as my personal account |
| **ngrok** | Creates a public URL that tunnels to my local machine |
| **GAM (Google Admin Manager)** | CLI tool to create/manage Google Workspace accounts |
| **Gmail API** | Drafts and schedules welcome emails |
| **AI engine (LLM)** | Reads Slack channels, makes decisions, triggers actions |
| **GitHub** | Stores all code and agent memory |

---

## How Each Piece Works

### Jeff-Bot (Slack Bot)
A Slack app I created at api.slack.com. It has a bot token that lets it post messages as "Jeff-Bot" instead of my personal account. It's joined to all IT support channels and the onboarding channels.

### Webhook Endpoint
A single API route in Next.js (`/api/webhooks/slack`) that receives every Slack event. It checks which channel the message came from and routes it to the right handler.

### ngrok Tunnel
My webhook runs on my local machine (localhost:3000). Slack needs a public URL to send events to. ngrok creates a permanent public URL (`my-domain.ngrok-free.dev`) that forwards traffic to my machine. As long as my machine is on and ngrok is running, Jeff-Bot is live.

### GAM
A command-line tool that connects to Google Workspace Admin. Instead of logging into admin.google.com and clicking through menus, I can create a full user account with one terminal command:
```bash
gam create user john.doe@company.com firstname John lastname Doe password random recoveryemail john@gmail.com
gam user john.doe@company.com add license 1010020025
```

### Agent Memory (agent-47 repo)
A GitHub repo that stores everything the AI learns over time — recurring issues per client, what fixes worked, environment quirks, open tickets. Every session reads from it and writes back to it. This is how the system gets smarter over time.

---

## Repositories

| Repo | What It Contains |
|------|-----------------|
| `mission-control` | The Next.js app that runs the webhook server |
| `jeff-bot` | The Slack webhook handler code (extracted) |
| `agent-47` | AI memory — client history, patterns, open tickets |
| `it-automation-suite` | This repo — project overview for portfolio |

---

## Workflow Example: New IT Ticket

1. Employee at Company A posts: *"My laptop won't connect to VPN, I can't work"*
2. Slack sends the event to my webhook URL
3. The webhook detects keywords: "can't work" → P2 HIGH
4. Jeff-Bot replies in the thread: *"Your request has been received and we're on it right away. Expect an update shortly."*
5. I get a summary DM with all new tickets across all 4 companies

Total time from ticket to acknowledgment: **under 3 seconds.**

---

## Workflow Example: New Hire

1. HR posts in `#onboarding-channel`: *"New hire: Jane Smith, React Developer, starts July 20, personal email: jane@gmail.com"*
2. Webhook detects it's a new hire channel
3. System creates `jane.smith@company.com` with a temporary password
4. Assigns Google Workspace Business Plus license
5. Adds `jane@gmail.com` as recovery email
6. Drafts welcome email with her credentials
7. Schedules the email for July 13 (7 days before start)
8. Sends me a private notification: *"New hire Jane Smith — Google account created, email scheduled for July 13"*

Total manual work required from me: **zero.**

---

## What I Learned

- How to set up Slack Event Subscriptions and build a bot from scratch
- How to expose a local server to the internet with ngrok
- How to use GAM to automate Google Workspace administration
- How to use the Gmail API to draft and schedule emails
- How to build a persistent AI memory system using GitHub
- How to connect multiple tools (Slack + Google + AI + GitHub) into a single automated workflow

---

## Running It Locally

### Prerequisites
- Node.js 22+
- pnpm
- ngrok account (free)
- Google Workspace Admin access
- GAM installed

### Start the server
```bash
cd mission-control
pnpm dev
```

### Start the tunnel
```bash
ngrok http --domain=your-domain.ngrok-free.dev 3000
```

### Environment Variables
```
SLACK_BOT_TOKEN=xoxb-your-bot-token
SLACK_SIGNING_SECRET=your-signing-secret
```
