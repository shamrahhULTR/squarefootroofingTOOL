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

## Foundation Finance plans

Plans live in the `financePlans` array right under the `BRAND` block. The APRs mirror
Foundation Finance's published credit tiers
(https://foundationfinance.com/perfect-credit-not-required/):

- Tier 1 (FICO 725+): est. 11.9% APR
- Tier 2: est. 13.5% APR
- Tier 3: est. 15.99% APR
- Tiers 4–5: est. 17.99% APR — approvals with FICOs as low as 550

Shipped plans: 10-year at each tier rate, 7- and 5-year at the Tier 1 rate, a 12-month
same-as-cash promo option (confirm current FFC promo availability before presenting),
and a Custom Plan for entering the APR + term from an actual approval. All APRs are
**presentation estimates** — the real rate comes from the customer's Foundation Finance
approval, and every screen carries a "subject to credit approval" notice.

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
