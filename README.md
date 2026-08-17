# Close Board — Square Foot Roofing

An offline-first, in-home roofing sales tool for iPads/tablets, built on the white-label
Roof Investment Planner skeleton (`roof-planner/`). Reps build up to three options
(Good / Better / Best), present a clean price build, show estimated monthly payments
through three lenders, and print a one page proposal. Deals
save locally on the device and can back up to a Google Sheet.

**Everything is one file:** `index.html`. There is no build step.

This copy ships branded for **Square Foot Roofing** (Virginia Beach, "We've Got You
Covered"): navy `#14265B/#203C8B` + orange `#F5821F` from their logo. The header shows
the tool as "The Investment Planner". The logo is embedded as a data URI in
`BRAND.logoUrl` so it works fully offline. The embedded copy is a reversed
(white knockout) variant so it sits flush on the navy header with no panel behind it.
`sfr-logo.png` is the original from squarefootroofing.com and
`sfr-logo-reversed.png` is the knockout; neither is loaded at runtime.

Good / Better / Best default to the SFR lineup: **SFR Architect Series** (architectural
fiberglass), **SFR Storm Series** (Class 4 storm rated), and **SFR Metal Series**
(premium metal), each with the full component stack described below.

## Options: Good, Better, Best

The three options are labeled **Good / Better / Best** everywhere (tabs, focus
selectors, proposal) rather than Option A/B/C. Labels live in `TIER_LABELS` at the top
of the options block. They default to SFR Architect Series (Good), SFR Storm Series
(Better), and SFR Metal Series (Best).

## Work included: the full warrantied system

Every line item lives in one `PRODUCTS` object that drives the rep checkbox, the
customer facing name, the scope list, and the warranty list, so the four can never
drift apart. 34 items covering the whole build: tear off, deck inspection and renail,
decking allowance, synthetic underlayment, ice & water shield, valley liner, sealed
penetrations, drip edge, step and counter flashing, valley metal, pipe boots and
collars, roof to wall flashing, kick out diverters, chimney flashing, chimney cricket,
ridge vent, soffit intake vents, off ridge and box vents, attic fan, starter strip,
hip and ridge caps, six nail fastening pattern, skylights, seamless gutters,
gutter guards, soffit and fascia, attic insulation, permit and final inspection,
magnetic nail sweep, manufacturer warranty registration, and the workmanship warranty.

20 of them are checked by default as the standard system (`FULL_SYSTEM_COMPONENTS`).
The rest are extras the rep checks per job. Adding a product to `PRODUCTS` is all it
takes to make it appear in every screen and on the printed proposal.

## Financing: three lenders

Plans live in `financePlans`, grouped by lender in the rep dropdown and shown as six
featured cards (two per lender) on the customer screen. Each card is labeled with its
lender. If the rep selects any other plan from the dropdown, that plan is added to the
customer screen too.

**Foundation Finance** writes out to **180 months**, which is now the default plan and
the featured lowest payment card. Tier estimates from their published rates: Tier 1
(FICO 725+) 11.9%, Tier 2 13.5%, Tier 3 15.99%, Tiers 4 to 5 17.99%, approvals down to
a 550 FICO. Plus the 12 month same as cash promo. Terms of 180, 120, 84, and 60 months.

**Acorn Finance** is a marketplace, not a single lender: one soft pull prequalification
returns offers from 30+ lending partners. Published range 6.24% to 35.99% APR, terms of
2 to 12 years (240 months for excellent credit), loans to $100,000, $0 dealer fees.
Prequalification does not affect the customer credit score.

**Synchrony** runs through a merchant account, so the exact plan numbers your account
carries come from your Synchrony agreement. Shipped plans mirror their published home
improvement structures: Plan 924 (18 month deferred interest, 26.99% standard APR),
Plan 971 (7.99% reduced rate with a fixed payment of 2.00% of the purchase amount),
a 12 month deferred interest option, and a 60 month reduced rate plan. **Confirm your
plan numbers before presenting.** The 2.00% fixed payment uses a `factor` pay type
rather than amortization, because that is how Synchrony actually bills it.

Deferred interest is disclosed on the customer screen: if any balance remains at the
end of the promo window, interest is charged back to the original purchase date. That
is not the same as 0% APR and the tool says so.

All APRs are **presentation estimates**. The real number comes from the customer
approval, every screen carries a "subject to credit approval" notice, and the
disclaimer notes that some lenders add an origination fee on smaller loans.

## No dashes

All customer facing and rep facing copy is written without em dashes, en dashes, or
hyphenated compounds. Dates render as "August 17, 2026" rather than ISO. The only
remaining dash characters are minus signs on discount amounts (`-$500`), which have to
stay for the price build to read correctly.

## Referral emails

The referral screen (after the deal) has two send actions that compose a ready to send
message **from `squarefootroofing@gmail.com`**:

- **Send ‹name› Their Free Inspection Info** goes to the referred friend: what the
  free inspection is, the three steps to book it, the office phone, and a note that
  financing is available.
- **Email ‹customer› Their Referral Confirmation** goes to the homeowner: how the
  $250 reward works and when it pays out.

Online, this opens Gmail compose pinned to the business account
(`authuser=squarefootroofing@gmail.com`) so the message genuinely sends from that
address. Offline, it falls back to a `mailto:` compose in the device's mail app. If the
referral contact is a phone number instead of an email, it opens a prefilled text
message. The rep always reviews and taps send, so nothing is sent automatically. Both
addresses live in the `BRAND` block (`officeEmail`, `officePhone`).

## Icons

`favicon.png` (512px) and `apple-touch-icon.png` (180px) are generated from the roof
mark in the real Square Foot logo, cropped out of `sfr-logo.png`, navy strokes knocked
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
2. Open the hosted planner once with `?backup=<your /exec URL>` on each rep device.
   The endpoint is stored locally and all pending deals sync automatically when online.
3. `?admin=1` shows the cloud self-test tools in the Deal Library.

## Rep workflow (4 buttons total: New Deal · Deals · Print · Present)

1. Fill in customer info; build Good / Better / Best (use the **Quick Price Helper**:
   squares × rate per square → Use as Retail Price).
2. Tap **Present** and internal math and rep controls disappear.
3. Guided flow: Project Investment → Compare Payments → Today's Offer → Decision.
4. Customer taps their option, picks a payment plan, then **Print**.
5. There is no Store button — deals auto-save on Present, Print, and New Deal
   (works with zero signal) and cloud-sync when back online.
