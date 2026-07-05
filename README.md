# Lakhani Foods Ltd — CRM

A self-contained CRM that runs entirely in your browser. No install, no server, no account.

## How to run
Double-click `index.html` (or keep using the preview server). All data is saved in your
browser's local storage on this device — export a backup from **Settings** regularly.

## What's inside
- **Analytics** — pipeline value, weighted forecast, revenue won, win rate, leads per month,
  stage funnel, outcomes donut, top companies, and cash + pipeline projection.
- **Lead Pipeline** — 5 stages with drag & drop: New Lead → Contacted → Sample Sent →
  Negotiation → Closed Won (each stage carries a win probability used in forecasts).
  Deals can also be marked Lost and reopened.
- **Contacts** — searchable contact book with full profile pages (role, company, deals, notes).
- **Companies** — company profiles with their people, deals, open pipeline and revenue won.
- **Banking** — "link" your account by entering a balance and importing a transactions CSV
  (`date, description, amount`). Shows projected balance if each deal / all deals / the
  weighted pipeline closes. Data never leaves this device.
- **Ask Claude to generate leads** — describe your target client base and Claude suggests
  prospect leads (company archetype, buyer persona, estimated value, rationale) that you can
  add straight into the pipeline. Requires an Anthropic API key (Settings), stored locally.
  Suggestions are research starting points — verify before outreach.
- **Password protection** — optional password (SHA-256 hashed, stored locally) with
  "remember this device for 30 days" so you don't sign in every time. Manage in Settings.

## Notes
- AI-suggested leads are tagged "AI suggested" in the pipeline.
- Clearing your browser's site data deletes the CRM data — use Settings → Export backup.
