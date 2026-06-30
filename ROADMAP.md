# Roadmap

A technical roadmap for the IT Automation Suite — where the system is today, what's being built next, and what the long-term vision looks like.

---

## Current State (v1.0)

The core automation pipeline is live and operational.

| Capability | Status |
|-----------|--------|
| Jeff-Bot auto-replies to IT tickets across 4 Slack workspaces | Live |
| Priority classification (P1–P4) based on message content | Live |
| Ticket tracking with sequential ticket numbers (SQLite) | Live |
| New hire detection from onboarding Slack channels | Live |
| Google Workspace account creation via GAM | Live |
| Welcome email drafted and scheduled via Gmail API | Live |
| Silent notification to private #newhire-process channel | Live |
| Persistent AI memory via agent-47 GitHub repo | Live |
| ngrok static tunnel — always-on public webhook URL | Live |

---

## Phase 2 — Reliability and Observability

The system works, but it's running on a local machine with no visibility into what's happening.

**Goal:** Know immediately when something breaks and why.

```
[ ] Uptime monitor
      Alert via Slack DM if the webhook stops responding
      Tools: UptimeRobot (free tier) → Slack incoming webhook

[ ] Structured logging
      Every event logged with timestamp, channel, client, action taken
      Store in SQLite alongside ticket records

[ ] Error alerting
      If GAM fails during account creation — DM Devin immediately
      If email scheduling fails — DM Devin with the hire details so it can be retried

[ ] Webhook retry handling
      Slack retries failed deliveries 3x — deduplicate by event_id to prevent double replies

[ ] Auto-restart on reboot
      Replace manual terminal commands with a launchd plist (Mac)
      Server and ngrok tunnel start automatically when the machine boots
```

---

## Phase 3 — Smarter Ticket Handling

Right now, auto-replies are based on keyword matching. The next step is context-aware responses.

**Goal:** Replies that feel like a real IT person wrote them, not a script.

```
[ ] AI-generated replies
      Route ticket text through an LLM API
      Reply is tailored to the specific issue, not a generic template
      Still includes ticket number and priority signal

[ ] Ticket deduplication
      Detect when the same user submits the same issue twice
      Reply: "This looks like a follow-up to Ticket #42 — we're still working on it."

[ ] Thread monitoring
      Watch for replies in open ticket threads
      If no IT response in X minutes → DM Devin as a reminder

[ ] SLA timers
      P1: 15 min response target
      P2: 1 hour
      P3: next business day
      P4: no commitment
      Escalation DM if timer is exceeded
```

---

## Phase 4 — Onboarding Intelligence

The new hire workflow is mostly automated. These additions close the remaining manual gaps.

**Goal:** Zero manual steps from HR post to first-day ready.

```
[ ] Slack invite automation
      Auto-invite new hire's work email to the Slack workspace
      Today this step is still manual

[ ] Group and channel assignment
      Parse the hire's role from the Slack message (e.g. "React Developer")
      Auto-add them to role-appropriate Slack channels and Google Groups

[ ] Equipment request trigger
      When a new hire is detected, automatically open an IT ticket:
      "Laptop needed for Jane Smith, starts July 20"
      Ticket routes back through the existing ticket system

[ ] Offboarding workflow
      Detect termination notices in Slack
      Suspend Google account, remove Slack access, revoke licenses
      Mirror of the onboarding flow, in reverse
```

---

## Phase 5 — Multi-Workspace Intelligence

Right now each workspace is handled independently. The system has no cross-client view.

**Goal:** A single command center for all 4 clients.

```
[ ] Unified ticket dashboard
      Web UI showing open tickets across all 4 clients
      Filter by client, priority, status, assignee

[ ] Cross-client pattern detection
      If the same issue appears in 3 out of 4 workspaces in one week → flag it
      Example: VPN outage hitting multiple companies at once

[ ] Weekly digest
      Auto-generated summary every Monday:
      - Tickets opened / closed last week per client
      - Any recurring issues
      - New hires starting this week
      Delivered as a Slack DM
```

---

## Phase 6 — Portability

The system is currently tied to one Mac. This phase makes it deployable anywhere.

**Goal:** Run on any machine or in the cloud with minimal setup.

```
[ ] Docker packaging
      Containerize the webhook server
      One command to spin up the full stack: docker compose up

[ ] Cloud hosting option
      Deploy to Railway or Fly.io (free tier)
      Eliminates dependency on local machine being on
      Removes need for ngrok entirely

[ ] Environment-based config
      All client IDs, channel IDs, and tokens loaded from .env
      No hardcoded values in source code

[ ] Secrets manager
      Replace .env file with 1Password CLI or similar
      Tokens rotate automatically
```

---

## Long-Term Vision

```
Slack
  └── Webhook
        ├── Ticket triage     → AI reply + SLA tracking
        ├── New hire          → Full account provisioning, zero manual steps
        ├── Offboarding       → Automated access revocation
        └── Anomaly detection → Flag unusual patterns across clients

All state in a database. All actions logged. All failures alerted.
Runs in the cloud. Restarts itself. Never goes offline.
```

---

## Version History

| Version | Date | What Shipped |
|---------|------|-------------|
| v1.0 | June 2026 | Jeff-Bot, ticket auto-reply, new hire onboarding, GAM integration, agent memory |
