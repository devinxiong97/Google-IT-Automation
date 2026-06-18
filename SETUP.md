# Setup Playbook

A complete step-by-step guide to replicate this IT automation system from scratch.

**Time to complete:** ~2 hours
**Prerequisites:** Admin access to Slack, Google Workspace, and a Mac

---

## Phase 1 — Create the Slack Bot

### 1.1 Create the app
1. Go to [api.slack.com/apps](https://api.slack.com/apps)
2. Click **Create New App** → **From scratch**
3. Name it (e.g. `Jeff-Bot`) → select your primary workspace → **Create App**

### 1.2 Add permissions
1. Go to **OAuth & Permissions** → scroll to **Bot Token Scopes**
2. Add these scopes:
   - `chat:write`
   - `channels:read`
   - `channels:history`
   - `channels:join`
   - `groups:read`
   - `groups:history`
   - `im:read`
   - `users:read`
3. Scroll up → click **Install to Workspace** → **Allow**
4. Copy the **Bot User OAuth Token** (`xoxb-...`) — save it somewhere safe

### 1.3 Get the signing secret
1. Go to **Basic Information**
2. Under **App Credentials**, copy the **Signing Secret**

---

## Phase 2 — Set Up the Webhook Server

### 2.1 Clone the repo
```bash
git clone https://github.com/your-username/mission-control
cd mission-control
pnpm install
```

### 2.2 Configure environment variables
Create a `.env` file in the project root:
```
SLACK_BOT_TOKEN=xoxb-your-bot-token-here
SLACK_SIGNING_SECRET=your-signing-secret-here
```

### 2.3 Start the server
```bash
pnpm dev
```
Server runs at `http://localhost:3000`

---

## Phase 3 — Expose Your Server with ngrok

### 3.1 Install ngrok
```bash
brew install ngrok
```

### 3.2 Create a free account
1. Sign up at [ngrok.com](https://ngrok.com)
2. Go to **Your Authtoken** in the dashboard
3. Run:
```bash
ngrok config add-authtoken YOUR_TOKEN_HERE
```

### 3.3 Claim a free static domain
1. Go to **dashboard.ngrok.com → Cloud Edge → Domains**
2. Claim your free static domain (e.g. `your-name.ngrok-free.app`)

### 3.4 Start the tunnel
```bash
ngrok http --domain=your-domain.ngrok-free.app 3000
```
Your webhook is now live at `https://your-domain.ngrok-free.app`

---

## Phase 4 — Connect Slack to Your Webhook

### 4.1 Enable Event Subscriptions
1. Go to **api.slack.com** → your app → **Event Subscriptions**
2. Toggle **Enable Events** on
3. Set Request URL to:
   `https://your-domain.ngrok-free.app/api/webhooks/slack`
4. Wait for **Verified** ✓

### 4.2 Subscribe to events
1. Click **Subscribe to bot events** → **Add Bot User Event**
2. Add: `message.channels` and `message.groups`
3. Click **Save Changes**

### 4.3 Reinstall the app
A yellow banner will appear — click **Reinstall your app** and approve

### 4.4 Invite the bot to your channels
In each channel you want the bot to monitor, type:
```
/invite @your-bot-name
```

For private channels, a member must invite it manually.

---

## Phase 5 — Set Up Google Admin Automation (GAM)

### 5.1 Install GAM
```bash
bash <(curl -s -S -L https://raw.githubusercontent.com/GAM-team/GAM/main/src/gam-install.sh)
```
Verify: `gam version`

### 5.2 Create a Google Cloud project for GAM
```bash
GAM_CONFIG_PATH=~/.gam/your-company gam create project
```
- Log in with your Google Workspace admin account when the browser opens

### 5.3 Authorize GAM
```bash
GAM_CONFIG_PATH=~/.gam/your-company gam oauth create
```
- Select **s** for all default scopes → **c** to continue
- Log in with your admin account
- Enter your admin email when prompted

### 5.4 Verify it works (read-only test)
```bash
GAM_CONFIG_PATH=~/.gam/your-company gam info domain
GAM_CONFIG_PATH=~/.gam/your-company gam print users | head
```
These commands only read data — nothing is changed.

---

## Phase 6 — Creating a New User

### Full command to create a user
```bash
GAM_CONFIG_PATH=~/.gam/your-company gam create user firstname.lastname@company.com \
  firstname FirstName \
  lastname LastName \
  password random \
  recoveryemail personal@gmail.com \
  changepassword true
```

### Add a license
```bash
# Find your license SKU first
GAM_CONFIG_PATH=~/.gam/your-company gam print licenses | grep -v "Got 0"

# Then assign it
GAM_CONFIG_PATH=~/.gam/your-company gam user firstname.lastname@company.com add license SKU_ID
```

### Set a known temporary password
```bash
GAM_CONFIG_PATH=~/.gam/your-company gam update user firstname.lastname@company.com \
  password "Company@2026!" \
  changepassword true
```

---

## Phase 7 — Welcome Email Template

### What the email looks like
```
Subject: Welcome To The Team - IT Onboarding

Welcome aboard, [First Name]

Here's everything you need to get set up on your first day.

Step 1 — Log into your Google account
  Email: firstname.lastname@company.com
  Temporary password: [generated password]

Step 2 — Join Slack
  Check your new work inbox for a Slack invitation.

Step 3 — Message your manager
  Once in Slack, find [Manager Name].

---
For IT help: it@company.com
```

### Sending rules
- Start date is **7+ days away** → send exactly 1 week before
- Start date is **less than 7 days away** → send immediately

---

## Phase 8 — Restart After Reboot

Every time your machine restarts, run these two commands in separate terminal tabs:

**Tab 1 — Start the server:**
```bash
cd ~/mission-control && pnpm dev
```

**Tab 2 — Start the tunnel:**
```bash
ngrok http --domain=your-domain.ngrok-free.app 3000
```

The bot is offline until both are running.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Bot not replying | Check ngrok is running and tunnel is connected |
| "channel_not_found" error | Bot hasn't joined the channel — use `/invite @bot-name` |
| GAM auth error | Re-run `gam oauth create` |
| Slack says URL not verified | Make sure `pnpm dev` is running before clicking Verify |
| Bot replies as wrong name | Reinstall the Slack app after updating the display name |
