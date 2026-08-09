# Lakhani's CRM — working notes

Read this first when picking the project up again. Written for whoever continues the build,
including future Claude sessions.

## What it is

A CRM for Lakhani's (Ru's food business — ready-to-use meal kits and frozen products, B2B trade
and consumer). One self-contained `index.html`, no build step, no server, no framework. All data
in `localStorage`, synced between devices through a private GitHub repo.

- **Repo:** `rl1311/lakhani-foods-crm` · **Live:** https://rl1311.github.io/lakhani-foods-crm/
- **Data store:** `rl1311/lakhani-foods-data` (private) — `data.json`, `backups/`, `docs/`
- **Devices:** two laptops and a phone, all installed as a PWA / app-mode shortcut

## The golden rule

**Editing the file on the laptop changes nothing the user sees.** The installed app loads from
GitHub Pages. Every change must be committed and pushed, and Pages takes 1–3 minutes to publish.
A whole round of "it isn't updating" was caused by forgetting this — don't repeat it.

Deploy = write file → `git add` → `git commit` → `git push origin main` → wait → verify.

**Bump `const BUILD` on every deploy.** It shows in the sidebar under the tagline, and it is how
the user confirms what is actually live. Format `YYYY-MM-DD.N`.

Note: `git push` through PowerShell prints its progress to stderr, which PowerShell renders as a
red `NativeCommandError`. That is **not** a failure — check for the `abc123..def456  main -> main`
line instead.

## Architecture

Single `<script>` block. Roughly: constants → store/persistence → sync → helpers → `App` object
(all UI actions, called from inline `onclick`) → `render*()` functions per view → boot.

- `store` holds: `contacts, companies, deals, orders, invoices, quotes, activities, docs,
  stock:{items, incoming}, bank, counters, settings, push, deleted`
- Adding a collection means touching **five** places: `freshData()`, `_collections()`,
  `buildPayload()`, `mergePayloads()`, `applyPayload()`. Miss one and it silently won't sync.
- Deletions are tracked in `store.deleted` so a record deleted on one device doesn't come back
  from another. Use `markDeleted(id)` before removing anything.
- `save()` stamps changes and queues a sync 8s later. `save(true)` means "this came from a sync",
  and must not re-stamp.

### Conventions that matter

- **Scratchpad pattern.** The Pricing tab models numbers but never writes until an explicit save
  button, with a confirm dialog naming exactly what will change. The user asked for this directly;
  keep it for anything similar.
- **Snapshot, don't recompute.** Invoices and quotes store `vatApplied` at issue time so turning
  VAT on later doesn't silently rewrite documents already sent. Same instinct applies anywhere a
  document has been given to a customer.
- **Semantic colour.** Use `var(--pos)` for "this number is good", `var(--red)` for bad,
  `var(--amber)` for warning. Don't reuse `--green-dark`; since the re-theme it is charcoal.
- **Never invent a deadline.** The stock "order by" date is derived from real deal close dates and
  says something weaker when they're absent. Don't fabricate dates to make output look richer.

### Brand

Charcoal `#26251e` · copper `#b0602a` · cream `#f5f0e6` · positive `#5a7247` · border `#e4dccb`.
Logo assets are embedded as base64 in `BRAND` (side / lock / doc / icon). Source artwork came from
`Lakhani Foods Logo Exploration.zip` in the user's Downloads. The green build before the re-theme
is kept as `index-green-backup.html`.

App icons need padding — Windows and Android mask-crop, and edge-to-edge artwork loses its corners.
`icon-*.png` are "any" (76% of canvas), `icon-*-maskable.png` are "maskable" (55%).

## Testing

Playwright, Chromium at `/opt/pw-browsers/chromium`. Serve first:

```
python3 -m http.server 8899 --directory /home/claude/crm
```

| File | Covers |
|---|---|
| `test3.js` | activities, next actions, close dates, quotes, reports |
| `test4.js` | VAT registration switch, per-document snapshots |
| `test5.js` | product documents, upload/download via a mocked GitHub API |
| `test6.js` | company documents, expiry states, buyer pack |
| `test7.js` | backups, restore, pruning, local export |
| `test8.js` | deal/customer pricing scenarios and forecast impact |
| `test9.js` | phone layout, drawer, sync without the passphrase |

~140 assertions total. Run all of them before deploying; they are fast and they have caught real
bugs (the pre-restore backup overwriting the backup being restored, for one).

Also worth doing: screenshot key views and actually look at them. Two design defects and the
clipped icon were only ever caught by eye.

## What's built

Analytics with next actions and overdue warnings · pipeline with drag/drop and due-state pills ·
contacts · companies · activity log (typed, dated, propagates deal → company → contact) ·
stock with pack sizes, MOQ, order multiple, lead time, reorder points and shortfall forecasting ·
reports (revenue and margin by customer and product, monthly, quiet-customer alerts) ·
pricing simulator (single product, and whole deal/customer with forecast impact) ·
quotes that convert to orders · orders · invoices with a VAT switch · banking · product and
company documents with expiry tracking · daily backups with restore · AI lead generation ·
password lock · two-way sync · phone layout.

## Backlog — in the order I'd do it

1. **Customer price lists.** Base trade price plus per-customer overrides and volume breaks. The
   biggest genuine gap for a wholesale business. Deliberately not built yet: it should be designed
   against two real customers paying different prices, which don't exist yet.
2. **Payment terms and credit per customer.** Terms are currently one global setting. Also worth
   knowing what a customer already owes before accepting the next order.
3. **Batch / lot numbers and best-before dates on stock.** Real for frozen and ambient food —
   traceability, withdrawal, and spotting short-dated stock. Depends on volumes not yet reached.
4. **Part-payments against an invoice.** A customer paying half is currently unrepresentable.
5. **Delivery notes / picking lists** for packing an order.
6. **Stacked table rows on phone.** Wide tables currently scroll sideways inside their card.
   Works, but cards would be better on a small screen.
7. **Logging an email into the record** from the inbox rather than retyping it.

## Known limitations — say these plainly, don't paper over them

- The app cannot attach files to an email; it hands off to the mail client. Download-then-attach
  is the real flow. It also can't merge PDFs — the spec sheet is generated, not stitched.
- The data repo is private, so document links in a quote aren't reachable by a buyer. Uploaded
  documents are listed as "attached / on request".
- Same-record edits on two devices between syncs resolve last-write-wins for that record. There is
  no field-level merge.
- The sync passphrase is unrecoverable by design (PBKDF2 150k + AES-GCM, only salt/iv/ciphertext
  stored). The route to a new device is Settings → **Show sync token** on a connected device.

## Context about the user's setup

- Desktop is redirected into OneDrive (`~/OneDrive - Lakhani Pub Company LTD/Attachments/Desktop`),
  so the folder picker can't grant it. Use Desktop Commander PowerShell to write there.
- The installed PWA broke once because uninstalling left dead shortcuts pointing at a removed Edge
  app ID, and the reinstall never registered. The working Desktop shortcut now uses
  `msedge.exe --app=<url>`, which doesn't depend on an app registration.
- Not VAT registered yet; expects to register later in 2026 on hitting the threshold. The switch is
  in Settings and must stay **off** until then — showing VAT before registration would be wrong.

## Where things stood at handover

The app is finished and stable; the business hasn't put data in yet. The user is finalising
products, costs and pricing and wanted everything ready first so they can enter it all in one go
and come back with a list of revisions. Next real input is that list — plus whatever a genuine
buyer's supplier approval form turns out to require, which should shape the documents work.
