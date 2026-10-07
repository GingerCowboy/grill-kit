# Grill: Amazon → eBay arbitrage app

> **Done (awaiting confirmation)** · 15 decided · 0 open · 0 deferred

## Summary

- **v1 is a paper validation** of ~20 UK deals, to prove margin exists before building. The full product later scans thousands of models.
- **Marketplaces:** amazon.co.uk → ebay.co.uk. Pass/fail uses business-seller figures; private-seller figures are shown for reference.
- **Strategy is hold, in small quantities.** General items are listed once Amazon is back at full price; LEGO after the set retires. Cut the price or exit after 90 days unsold. Selling straight after buying is dropped.
- **Pass rule:** at least £10 net per item and at least a 30% return on cost, after eBay fees, postage, packaging and returns. Deals are ranked by £ net per item.
  - Buy price under £33.33: the eBay price (S) must be at least `1.095 × buy + £16.96`.
  - Buy price £33.33 or more: S must be at least `1.42 × buy + £6.00`.
  - Both assume a 6.9% final value fee.
- **Sample:**
  - About 10 top drops from Keepa's UK deals page: at least 40% below the usual price, mixed categories, buy price £30–200.
  - About 10 LEGO sets or toys near retirement.
- **Data:**
  - Amazon side: Keepa's free charts by hand. The Keepa API (~€49/month) is only needed if validation passes.
  - eBay active listings: eBay's official MCP server (Browse API), plus an application for Marketplace Insights.
  - eBay sold prices: confirmed by hand in Terapeak, with no AI automation.
  - App pipeline: automated screen → manual Terapeak check → Top 10.
- **Matching:** barcode first, then LEGO set number or model number. Anything else is flagged for a manual check.
- **Output:** an .xlsx with the formulas built in. It shows private and business columns, pass/fail, and a sorted Top 10.
- **Accepted open items:**
  - The smart-speaker final value fee is unconfirmed (6.9% vs 9.9%).
  - Insights approval is uncertain.
  - A new product generation could force a clearance and sink held stock.
  - The HMRC £1,000 trading allowance means Self Assessment once sales pass it.
  - The Echo Dot at £29.99 likely fails: eBay buyers would need to pay at least £49.81.

## Decisions

### Q1 · Purpose of v1
- **Answer:** Validate first: a one-off analysis of ~20 current deals to prove margin exists before building an app.
- **Why:** took recommendation
- **Unblocked:** pass bar for validation; how to pick the sample. Closed: multi-user/product branches (accounts, scale); recurring report cadence is now post-validation.

### Q2 · Region
- **Answer:** UK: amazon.co.uk → ebay.co.uk.
- **Why:** user lives in the UK.
- **Unblocked:** UK seller status, UK fees/postage/tax facts. Closed: cross-border (duty, international shipping).

### Q3 · Flip strategy
- **Answer:** Hold: buy on deep Amazon sales, hold small quantities, and sell in Amazon's full-price windows, but only when profit per item is high enough. Immediate flip dropped. Any product category is fine; the full product is a deep search across thousands of models.
- **Why:** user treats holding as cost-free at small quantities; analysis showed flip loses (other retailers match sale prices).
- **Unblocked:** ranking/profit floor; sell-timing rule for holds; data access at scale (thousands of models); sample can span categories. Accepted risk: new-generation clearance can wipe out held stock.

### Q4 · Pass bar
- **Answer:** A deal passes at ≥30% ROI and ≥£5 net per unit, after eBay fees, postage, packaging and returns, before income tax.
- **Why:** took recommendation
- **Unblocked:** go/no-go rule for building the app; "Top 10" ranking can filter on this bar.

### Q8 · Ranking and profit floor
- **Answer:** Rank deals by net £ profit per item (business-seller figures). A deal must clear both ≥£10 net per item and ≥30% ROI (raises Q4's £5 floor).
- **Why:** took recommendation; ~15–20 min of effort per item, so £10 ≈ £30–40/hour.
- **Unblocked:** pass formula updated (Facts → Profit model).

### Q5 · eBay data source
- **Answer:** Use eBay's official MCP server with the user's developer keys/login for active UK listings (Browse API, by barcode), and apply to eBay for Marketplace Insights access to automate sold prices.
- **Why:** user asked whether the MCP could use their login; chose to also try for Insights access.
- **Unblocked:** product matching by barcode (GTIN). Insights approval is uncertain and slow; until then sold prices come from Terapeak / Product Research (free, 3 years, manual).

### Q6 · Seller status
- **Answer:** Private seller while in the testing stage.
- **Why:** user's choice for the testing stage.
- **Unblocked:** challenged in Q7.

### Q7 · Seller status challenge
- **Answer:** Model both private and business columns; pass/fail is judged on business-seller figures.
- **Why:** took recommendation (v1 is a paper analysis, so private status saves nothing while testing; the real app must run as a business seller under eBay policy).
- **Unblocked:** profit formula fixed (see Facts → Profit model).

### Q9 · Sold prices at scale
- **Answer:** User proposed an AI driving the browser on Terapeak; resolved by Q10.
- **Why:** Terapeak is manual and can't cover thousands of models if eBay refuses Insights.
- **Unblocked:** challenged in Q10 (eBay User Agreement bans automated access, naming AI agents since Feb 2026).

### Q10 · Terapeak automation challenge
- **Answer:** Two-stage: the app screens thousands of models using active-listing prices (Browse API); the user confirms the top ~20 by hand in Terapeak (~20 min). No AI-driven Terapeak.
- **Why:** took recommendation; stays within eBay's terms and protects the seller account.
- **Unblocked:** app pipeline shape: automated screen → manual sold-price check → final Top 10. Insights approval, if granted, replaces the manual step.

### Q11 · Amazon-side data
- **Answer:** Keepa's free website charts by hand for validation; subscribe to the Keepa API (~€49/month) only if validation passes.
- **Why:** took recommendation
- **Unblocked:** sample selection (Keepa deals page); product matching (Keepa shows EAN/UPC). Closed: Creators API.

### Q12 · Validation sample
- **Answer:** Both: ~10 top drops (≥40% below usual price) from Keepa's UK deals page, mixed categories, buy price £30–200; plus ~10 LEGO/toys, favouring sets near retirement.
- **Why:** user picked both options; the 10/10 split is Claude's assumption.
- **Unblocked:** sell-timing may differ by category (LEGO retirement vs electronics full-price windows); LEGO matches cleanly by set number.

### Q13 · Sell-timing rule
- **Answer:** Per category. General items: list once Amazon is back at full price. LEGO: list after the set retires. Both: cut the price or exit after 90 days unsold.
- **Why:** took recommendation
- **Unblocked:** validation uses eBay sold prices from Amazon full-price periods (general) and post-retirement periods (LEGO) as each item's expected sale price.

### Q14 · Product matching
- **Answer:** Match by barcode (EAN/UPC) first, then LEGO set number or model number; anything else is flagged for a manual check.
- **Why:** took recommendation; wrong matches (colour, bundle, region variants) create fake profit.
- **Unblocked:** nothing new.

### Q15 · Validation output
- **Answer:** A spreadsheet (.xlsx) with the profit formulas built in. The user types in prices; it shows private and business net, pass/fail and a sorted Top 10.
- **Why:** took recommendation
- **Unblocked:** nothing new.

## Frontier

_Empty._
- Validation output: spreadsheet, script printout, or markdown report?

## Waiting on

_Nothing._

## Deferred

_None._

## Facts

### Profit model (business seller, not VAT-registered)

- `fees = 1.2 × (r × S + 0.0035 × S + £0.40)` where S = buyer's total (free-postage listing), r = final value fee rate (6.9% or 9.9%, unconfirmed)
- `net = S − fees − postage (£3.75 Tracked 48) − packaging (£0.50, assumed) − returns (£0.75) − buy price`
- At r = 6.9%: `net = 0.913 × S − 5.48 − buy`
- Pass rule (Q8: ≥£10 and ≥30% ROI): `S ≥ 1.42 × buy + £6.00` when buy ≥ £33.33 (ROI binds); `S ≥ 1.095 × buy + £16.96` when buy < £33.33 (£10 floor binds). Echo Dot at £29.99 needs S ≥ £49.81.
- Q4's original rule (≥£5 and ≥30%) was `S ≥ 1.42 × buy + £6.00` for buy ≥ £16.67
- Private column: `buyer total = 1.04 × item price + £3.64` (Buyer Protection fee + £2.94 label paid by buyer); `net = item price − £0.50 − buy`

### Q3 analysis: Echo Dot 5th gen, buy at £29.99 (current sale floor)

| Strategy | eBay buyer pays | Net, business 6.9% | ROI | Net, business 9.9% | Net, private |
|---|---|---|---|---|---|
| Flip (during sale) | £32 | −£6.25 | −21% | −£7.41 | −£3.22 |
| Flip (during sale) | £40 | £1.05 | 4% | −£0.39 | £4.47 |
| Hold (Amazon at £54.99) | £42 | £2.88 | 10% | £1.36 | £6.39 |
| Hold (Amazon at £54.99) | £49 | £9.27 | 31% ✅ | £7.50 | £13.13 |

- Hold passes the bar only if eBay buyers pay ≥ ~£48.71 (6.9%) or ≥ ~£50.71 (9.9%): within ~£4–6 of Amazon's own new price, with no Amazon warranty. Observed eBay asking prices are £37–49, so this is unlikely for Echo Dots at a £29.99 buy.
- At the 2023–24 floor (£22.99), hold would pass at S ≥ ~£38.70, which is plausible. The floor has since risen to £29.99.
- Flip loses: Argos, Currys and John Lewis match Amazon sale prices on the same days, so eBay buyers can get a new unit at the sale price.
- Biggest risk to hold: a new Dot generation. The 4th gen was cleared at £19.99 (−60%) around the 5th gen launch in Sep 2022, then withdrawn.
- Confidence: high on the Amazon timeline, low on eBay prices (asking prices only, no sold data). Exact figures need Terapeak sold prices split into Amazon-sale vs full-price periods.

### Amazon UK Echo Dot 5th gen timeline (Keepa chart + hotukdeals; lookup 2026-10-07)

- List £54.99 throughout. Main sale lows: £21.99 (2023), £22.99 (2024), £29.99 (2025–26)
- 2026 so far: £54.99 from 2 Jan to 10 May (no spring sale); £29.99 Prime Day 23–26 Jun; £39.99 25 Aug–2 Sep; £29.99 29 Sep–7 Oct
- Share of the year at full price: ~55% (2023), 60% (2024), 75% (2025), 80% (2026 so far), in stretches of 1–4 months
- Echo Dot Max (Sep 2025) and Alexa+ UK (Mar 2026) didn't visibly hurt 5th gen prices; no 5th-gen successor found (weak)
- Sources: `https://graph.keepa.com/pricehistory.png?asin=B09B8YWXDF&domain=co.uk&amazon=1&range=1500`, hotukdeals deal posts

### UK (lookup 2026-10-07; via search snippets, official pages not fetched directly)

- **eBay policy:** buying items to resell requires registering as a business seller (`https://www.ebay.co.uk/help/policies/selling-policies/selling-practices-policy/business-seller-policy?id=4710`)
- **Private sellers:** no final value fee since Oct 2024; buyer pays a Buyer Protection fee (£0.10 + 7% to £20 + 4% £20–300 …) (`https://www.ebay.co.uk/help/buying/paying-items/buyer-protection-fee?id=5594`)
- **Business sellers:** final value fee on total incl. postage, plus £0.40/order (>£10), plus 0.35% regulatory fee, plus 20% VAT on fees (`https://www.ebay.co.uk/sellercentre/news/2026-january/rate-card-change`). Smart speakers rate uncertain: ~6.9% (third-party calculator) vs 9.9% general Sound & Vision.
- **Postage:** Royal Mail Tracked 48 small parcel £3.75 online (from 5 Oct 2026); Evri 0–1 kg £3.04 home / £2.62 ParcelShop
- **Returns:** business sellers must accept 14-day cancellations for any reason (Consumer Contracts Regulations); opened units can't resell as sealed. ~5% returns at ~£15 each ≈ £0.75/unit.
- **HMRC:** buying to resell for profit is likely trading; £1,000 gross trading allowance per tax year (≈25 sales at £40); platforms report sellers with 30+ sales or ~£1,700/year to HMRC (`https://www.gov.uk/guidance/reporting-rules-for-digital-platforms`)
- **eBay UK new/sealed Echo Dot 5th gen:** asking ~£37–49; one at £39.89 while Amazon was at £54.99 (Apr 2025, weak); no sold data found
- **Keepa** supports amazon.co.uk; API from ~€49/month
- **Creators API** works for amazon.co.uk but needs 10 qualifying sales/30 days, and Associates content may be used "solely" to drive traffic to Amazon: sourcing eBay resales likely breaches the terms
- **Terapeak / Product Research:** free in eBay UK Seller Hub, 3 years of sold data; needs at least one sale to opt in (`https://www.ebay.co.uk/sellercentre/news/2024-october/product-research-tool`)

### Region-independent

- Amazon PA-API 5 retired 2026-05-15 (`https://affiliate-program.amazon.com/creatorsapi/docs/en-us/paapiv5-deprecation`)
- Amazon Conditions of Use prohibit scraping prices/listings
- eBay Finding API (incl. `findCompletedItems`) decommissioned 2025-02-05 (`https://developer.ebay.com/develop/apis/api-deprecation-status`)
- eBay Marketplace Insights API (sold items, 90 days) is Limited Release needing eBay approval
- eBay Browse API: active listings only; supports GTIN search
- eBay User Agreement bans scrapers/bots and, since 2026-02-20, LLM agents, without permission; official APIs are the permitted route (`https://www.ebay.com/help/policies/member-behaviour-policies/user-agreement?id=5414`)
- eBay's official MCP server (`eBay/npm-public-api-mcp`) is a generic, GET-only wrapper over eBay REST APIs using developer app keys or a user refresh token; it doesn't widen API access (`https://github.com/eBay/npm-public-api-mcp`)
- Amazon device warranty covers buyers from Amazon/authorised resellers only; eBay buyers may get none
