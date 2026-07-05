# Lakhani Foods Ltd — CRM

A self-contained CRM that runs entirely in your browser. No install cost, no server, no account.

**Live app:** https://rl1311.github.io/lakhani-foods-crm/

## Install as a desktop app (both laptops)
1. Open the live link above in **Edge** or **Chrome**.
2. Edge: menu (…) → **Apps** → **Install this site as an app**. Chrome: install icon in the address bar.
3. The CRM gets its own window, taskbar/Start-menu icon, and works **offline** after the first load.

All data is saved in the browser's local storage on each device — export a backup from
**Settings** regularly. Data does not sync between laptops; use Export/Restore to move it.

## What's inside
- **Analytics** — pipeline value, weighted forecast, revenue won, win rate, leads per month,
  stage funnel, outcomes donut, top companies, and cash + pipeline projection.
- **Lead Pipeline** — 5 stages with drag & drop: New Lead → Contacted → Sample Sent →
  Negotiation → Closed Won (each stage carries a win probability used in forecasts).
  Deals can also be marked Lost and reopened.
- **Contacts** — searchable contact book with full profile pages (role, company, deals, notes).
- **Companies** — company profiles with their people, deals, open pipeline and revenue won.
- **Stock** — track products with on-hand levels, link required quantities to deals, log
  incoming supplier deliveries with expected dates, and see per-product status: available now
  (after stock committed to won deals), pipeline demand, incoming, and projected position if
  every open deal closes — with explicit shortfall warnings and how much more to order.
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
