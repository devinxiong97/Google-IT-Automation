# IT New-Hire Provisioning Checklist

Internal checklist for provisioning accounts when a new employee is onboarded.
Work through it top to bottom for each hire. It also serves as a training
reference for new IT team members learning the onboarding process.

> Example documentation. Org names, domains, and contacts are placeholders
> (`OrgA`, `example.com`, `accounting@example.com`).

## Before you start — gather from the HR intake

- Full name
- Job title
- Company / entity (e.g. `OrgA`, `OrgB`, …)
- Manager
- Work type (Remote / In-office / Hybrid)
- Start date
- Personal email (for the welcome message)

> **Admin access is restricted.** Only designated admins per entity hold
> Slack/Google privileges. Never grant admin to a new hire. If you're unsure which
> entity a hire belongs to, confirm before creating any accounts.

## Checklist

- [ ] Confirm which entity the hire belongs to
- [ ] Create & license the Google Workspace account
- [ ] Invite to Slack (correct workspace)
- [ ] Add to 1Password
- [ ] Send the welcome / first-steps email
- [ ] Remote hires only: kick off the corporate-card + laptop process

## Step-by-step

### 1. Confirm the entity

Each hire belongs to **exactly one** entity. This determines their email domain,
which Slack workspace they join, and which Google Workspace tenant their account
lives in. If there's any doubt, confirm before creating anything — don't guess.

### 2. Create & license the Google Workspace account

- Sign in to the Google Admin Console for that entity's tenant.
- Go to **Directory → Users → Add new user**.
- Use the format `firstname@<entity-domain>`.
- Set a temporary password and require a change on first sign-in.
- **Assign/verify a license** so mail and Drive are active.
- Save the email + temporary password for the welcome email.

### 3. Invite to Slack

- Open the entity's Slack workspace as an admin.
- Invite the hire using their new **work email**.
- Set their **full name** in both the *Name* and *Display name* fields, and append
  their job title in parentheses to the display name.
- Add them to the default/team channels for their department.
- **Do not grant admin privileges.**

### 4. Add to 1Password

- In the 1Password admin console, invite the hire by their **work email**.
- Assign them to the appropriate **group / vaults** for their role and team.
- Confirm they accept the invite and complete account setup.

### 5. Send the welcome email

- Send the standard first-day email with their Google credentials and Slack/manager
  steps.

### 6. Remote hires only — corporate card + laptop

- For remote employees, kick off the laptop process (see
  `remote-onboarding-laptop.md`).
