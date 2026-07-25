# Close Board — Square Foot Roofing

An offline-first, in-home roofing sales tool for iPads/tablets, built on the white-label
Roof Investment Planner skeleton (`roof-planner/`). Reps build up to three options
(Good / Better / Best), present a clean price build with discounts, show estimated
monthly payments through **Foundation Finance**, and print a one-page proposal. Deals
save locally on the device and can back up to a Google Sheet.

**Everything is one file:** `index.html`. There is no build step.

This copy ships branded for **Square Foot Roofing** (Virginia Beach — "We've Got You
Covered"): navy `#14265B/#203C8B` + orange `#F5821F` from their logo. The header shows
the tool as "The Investment Planner". The logo is embedded as a data URI in
`BRAND.logoUrl` so it works fully offline — the embedded copy is a reversed
(white-knockout) variant so it sits flush on the navy header with no panel behind it.
`sfr-logo.png` is the original from squarefootroofing.com and
`sfr-logo-reversed.png` is the knockout; neither is loaded at runtime.

Options A/B/C default to the SFR lineup: **SFR Architect Series** (architectural
fiberglass), **SFR Storm Series** (Class 4 storm-rated), and **SFR Metal Series**
(premium metal), each with the full component stack (tear-off, synthetic underlayment,
ice & water, starter & ridge caps, ridge vent, drip edge, pipe boots).

## Financing — Foundation Finance + Acorn Finance

Plans live in the `financePlans` array right under the `BRAND` block, grouped by lender
in the rep dropdown and shown as six cards (3 per lender) on the customer financing
screen. Every card carries its lender name so the two are never confused.

**Foundation Finance** — APRs mirror their published credit tiers
(https://foundationfinance.com/perfect-credit-not-required/):
Tier 1 (FICO 725+) 11.9% · Tier 2 13.5% · Tier 3 15.99% · Tiers 4–5 17.99%, with
approvals down to a 550 FICO. Plus a 12-month same-as-cash promo option (confirm
current availability before presenting).

**Acorn Finance** — a marketplace, not a single lender: one soft-pull prequalification
returns offers from 30+ lending partners (https://acornfinance.com/contractors/).
Published range is **6.24%–35.99% APR**, terms of 2–12 years (up to 240 months for
excellent credit), loans to **$100,000**, and **$0 dealer fees** to the contractor.
Prequalification does not affect the customer's credit score; a hard pull only happens
once they pick a lender. Shipped plans: 12-year (est. 12.99%), 10-year (est. 11.99%),
a best-rate card at their published 6.24% floor, plus 7-year, 5-year, and a 20-year
extended term.

All APRs in the tool are **presentation estimates** — the real number comes from the
customer's own approval, every screen carries a "subject to credit approval" notice,
and the disclaimer notes that some lenders add an origination fee on smaller loans
(Acorn lists 1–6% under $40,000). Use the **Custom Plan** to enter the APR and term
from an actual approval.

## Referral emails

The referral screen (after the deal) has two send actions that compose a ready-to-send
message **from `squarefootroofing@gmail.com`**:

- **Send ‹name› Their Free Inspection Info** — goes to the referred friend: what the
  free inspection is, the three steps to book it, the office phone, and a note that
  financing is available through both lenders.
- **Email ‹customer› Their Referral Confirmation** — goes to the homeowner: how the
  $250 reward works and when it pays out.

Online, this opens Gmail compose pinned to the business account
(`authuser=squarefootroofing@gmail.com`) so the message genuinely sends from that
address. Offline, it falls back to a `mailto:` compose in the device's mail app. If the
referral contact is a phone number instead of an email, it opens a pre-filled text
message. The rep always reviews and taps send — nothing is sent automatically. Both
addresses live in the `BRAND` block (`officeEmail`, `officePhone`).

## Icons

`favicon.png` (512px) and `apple-touch-icon.png` (180px) are generated from the roof
mark in the real Square Foot logo — cropped out of `sfr-logo.png`, navy strokes knocked
to white, set on a rounded `#14265B` plate so it reads at 16px. Both are referenced in
the `<head>`, listed in the manifest, and precached by the service worker.

## Deploying

Drag the `close-board` folder into Netlify (or any static host). HTTPS hosting enables
the installable offline field app (service worker + manifest are included). Saved deals
are keyed per company name (`square-foot-roofing`), so this deploy never mixes data
with other brands even on the same device.

## Google Sheets deal backup (optional)

1. Create a Google Apps Script bound to a Sheet with a `doPost(e)` that parses
   `JSON.parse(e.postData.contents)` and appends `dealData` fields as a row, then
   deploy it as a web app (`/exec` URL, access: anyone).
2. Open the hosted planner once with `?backup=<your /exec URL>` on each rep device —
   the endpoint is stored locally and all pending deals sync automatically when online.
3. `?admin=1` shows the cloud self-test tools in the Deal Library.

## Rep workflow (4 buttons total: New Deal · Deals · Print · Present)

1. Fill in customer info; build Options A/B/C (use the **Quick Price Helper**:
   squares × rate per square → Use as Retail Price).
2. Tap **Present** — internal math and rep controls disappear.
3. Guided flow: Project Investment → Compare Payments → Today's Offer → Decision.
4. Customer taps their option, picks a payment plan, then **Print**.
5. There is no Store button — deals auto-save on Present, Print, and New Deal
   (works with zero signal) and cloud-sync when back online.
