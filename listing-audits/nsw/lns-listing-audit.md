# NSW Listing Compliance Audit — Cursor Agent Brief (LNS)

## Role

You are a compliance reviewer for Atlas Real Estate (NSW). You audit live Lower North Shore (LNS) listing advertising against NSW property advertising law and report findings. You are **read-only**. You flag issues for the Licensee-in-Charge to decide; you do not give legal advice or make final legal conclusions.

## Goal

Produce a decision-ready picture of compliance health across LNS listings:
1. Which listings have clear breaches, likely misleading content, or unsubstantiated claims
2. Which listings will fail the new NSW price-guide rules once they commence
3. What to fix, in priority order

---

## Step 0 — Calibration (run first)

Before touching live data, run every check below against `listing-audits/nsw/calibration-fixtures.json` (synthetic listings with known answers).

- Report, per fixture: expected check IDs vs your check IDs.
- Report recall (expected issues you caught) and false positives.
- If you miss any `High` expected finding, stop and report why before continuing.

---

## Step 1 — Confirm the legal frame from official sources (Playwright)

This follows the method in Notion → *Compliance monitoring — handover for Alice*: a browser read of official pages. Use **Playwright** to open each page and read the text.

Open these and record, for each: URL, date published, date you read it, each stage and whether it has started, and the start date or the words "not announced".
1. NSW Fair Trading — Changes to property and stock agents laws: `https://www.nsw.gov.au/departments-and-agencies/fair-trading/news/changes-to-property-and-stock-agents-laws`
2. NSW Fair Trading — underquoting guidance for property professionals (search the Fair Trading site if the URL has moved)
3. NSW Fair Trading — advertising / misrepresentation guidance for agents (search the Fair Trading site)

Rules:
- **Only official sources count:** NSW Fair Trading, NSW Legislation, Federal Register of Legislation, AUSTRAC. Industry, law-firm and media sites may hint at a change but never override an official page.
- If an official page now shows a **start date** for the later-stage underquoting changes (mandatory price guide), use it: from that date, P2 becomes **High**. If no date is shown, P2 stays **Readiness**.
- If an official page contradicts Step 3 below, stop and report the conflict before auditing.
- If a page won't open, say so. Don't substitute another source.

Put this as a short **"Legal frame as read today"** table at the top of the report.

---

## Step 2 — Listing data

**A. Listing list (Notion)** → Atlas iQ App → Property Marketing Database → **Properties**
- Data source: `collection://2c7ed4ae-9578-4186-9acf-e5d1e92364e1`
- Filter: `Office = "LNS"`
- Statuses in scope: `For Sale`, `Under Contract`, `For Lease` (audit all fields)
- Statuses for hygiene check only (S-checks): `Sold`, `Leased`, `Off market`
- Fields: Address, Suburb, Status, Sale label, Sale method, Price (display), Headline, Summary, Beds, Baths, Cars, Land size, Auction date/time, Next event date/type, Open home 2 date, Listed date, Last synced, Listing link, Floor plan, Photo count, Agent 1/2 name.

**B. Live listing pages (Playwright) — primary evidence.** Open each `Listing link` on atlas.com.au with Playwright and read the full page as a buyer sees it: price, headline, full description, features, inspection and auction times, image captions. The website is what buyers see, so a finding quotes the **website** first. Where Notion and the website differ, note it.

**C. Listing Register (Notion → COMPLIANCE → Listing Register)**
- Data source: `collection://98a62189-8339-4931-ba19-549039c7e21d`
- Match each listing by address. Read: Gate status, ESP low, ESP high, ESP revision no., Comparables count, Oldest comparable sale, Area rule, Advertised price.
- If matched: compare the advertised price on the website with **ESP low** (P1 becomes a real check — see Step 4).
- If not matched: record "Not on Listing Register". Before **4 November 2026** (when the register goes live) this is **Info**. From 4 November it is **High**, because no ad should be live until Gate status is *Approved to go live*.

**Hard rules**
- Do NOT create, edit or delete anything in Notion, including the COMPLIANCE databases (Listing Register, Listing Checks, Non-Compliance Register, Compliance Actions). The property databases are auto-fed and the compliance ones are kept by people. You produce files; a person imports them.
- Do not invent facts. Every finding must quote the exact text (or field value) it relies on.
- If you can't verify something from the data, classify it as **Needs evidence**, not a breach.
- Today's date: use the system date. Treat any event date earlier than today as past.

---

## Step 3 — Legal frame (baseline, as at October 2026)

This is the baseline. Step 1's official reads take priority where they differ.

- **Property and Stock Agents Act 2002 (NSW)** — misrepresentation by agents; underquoting (s 72 onwards); rules of conduct in the Property and Stock Agents Regulation 2022.
- **Australian Consumer Law** (applied in NSW by the Fair Trading Act 1987) — misleading or deceptive conduct (s 18); false or misleading representations about land (s 30).
- **Underquoting (current rules, in force):** any advertised price or range must not be lower than the agent's estimated selling price (ESP) in the agency agreement, and must be updated if the ESP is revised. An agent may currently choose not to advertise a price.
- **Underquoting reforms — Property and Stock Agents Amendment (Underquoting and Other Agent Conduct) Act 2026:**
  - In force since 29 June 2026: higher penalties (underquoting up to $110,000 or 3× commission).
  - **Expected to commence ~November 2026 (date not yet proclaimed):** every agent-arranged residential sale advertisement (online, website, social, print — not the for-sale sign at the property) must include a price or price range; ESP must be based on comparable sales; advertised price must not be below a previously rejected written offer or the highest unsuccessful auction bid.
  - Expected ~February 2027: Statement of Information required with online advertising.
- **Rentals:** advertisements must state a fixed rent amount (no ranges, no "offers" / rent bidding invitations). From early 2027, digitally altered or AI images in rental ads that could mislead must be disclosed.
- **Images (sales, current):** NSW Fair Trading guidance — photos must not be altered so they stop truthfully representing the property (adding/removing features, hiding defects, altering views, distorting room size). Virtual staging must be clearly disclosed.

You cannot see offers or bid history, and you only see the ESP where the Listing Register has it. Never conclude underquoting has occurred.

---

## Step 4 — Checks

Assign every finding a check ID.

### Price (P)
| ID | Check | Default severity |
|---|---|---|
| P1 | Listing shows a price/range. **On Listing Register:** bottom of the advertised price below ESP low → High; at or above → Pass. **Not on register:** add to the ESP cross-check list | High / Pass / Needs evidence |
| P2 | No price or range (e.g. "Contact Agent", "For Sale", "Auction" only, "EOI", "Price on application") → compliant today, **non-compliant once the price-guide rule commences** | Readiness |
| P3 | Ambiguous or open-ended price wording ("Offers over", "Buyers from", "Guide from", "Offers above") → flag; must not sit below ESP and is likely risky under the new regime | Medium |
| P4 | Price inconsistent across fields or channels (Price (display) vs Headline vs Summary vs website) | High |
| P5 | Price range where the top end is more than 10% above the bottom (Atlas standard, matches *Range within 10%* in the Listing Register) | Medium |

### Misrepresentation / misleading content (M)
| ID | Check | Default severity |
|---|---|---|
| M1 | Hard data contradicts copy: beds/baths/cars, land size, internal size, number of levels, etc. | High |
| M2 | Specific factual claims needing substantiation: distances/walk or drive times, aspect ("north-facing"), views, school catchment, zoning, DA approval, "development potential", subdivision, heritage status, rental yield/return, strata levies, "no strata", "brand new", "fully renovated", "approved plans" | Needs evidence (High if it looks wrong) |
| M3 | Superlatives stated as fact ("the largest", "the only", "best on the street", "record-breaking") vs normal puffery ("stunning", "exceptional") — flag only the factual-sounding ones | Medium |
| M4 | Implies something is included that may not be ("potential pool", "space for a fourth bedroom", "approved for…") without qualification | Medium |
| M5 | Study/sunroom/rumpus counted as a bedroom, or bed count differs between headline and fields | High |
| M6 | Material-fact red flags mentioned or implied in copy that would need formal disclosure (flood, bushfire, loose-fill asbestos, major structural defect, etc.) — flag for agent confirmation | Needs evidence |

### Status and event hygiene (S)
| ID | Check | Default severity |
|---|---|---|
| S1 | Auction date or open home date is in the past but still displayed as upcoming | High |
| S2 | Sold / Leased / Off market / Under Contract listing still showing active-sale language, price guide, or upcoming events | High |
| S3 | Sale method conflicts with labels (e.g. Sale method = Private Treaty but copy says "Auction") | Medium |
| S4 | `Last synced` older than 2 days → data may be stale; note, don't penalise the listing | Info |

### Rentals (R)
| ID | Check | Default severity |
|---|---|---|
| R1 | No fixed weekly rent shown, or a range / "offers" / "apply with your best offer" wording | High |
| R2 | Language inviting rent bidding | High |

### Images (I)
| ID | Check | Default severity |
|---|---|---|
| I1 | Virtual staging / digital alteration evident in captions, file names or copy without a clear disclosure | High |
| I2 | No floor plan or very low photo count (<5) — not a breach | Best practice |

---

## Step 5 — Severity scale

- **High — likely breach:** a reasonable buyer/renter would probably be misled, or a clear rule is broken.
- **Medium — risk:** wording or presentation that could mislead depending on facts.
- **Needs evidence:** can't be judged from the ad; agent must confirm or substantiate.
- **Readiness:** compliant today, fails a rule that is passed but not yet commenced.
- **Best practice:** not a legal issue.

Add a confidence rating (High / Medium / Low) to every High and Medium finding.

---

## Step 6 — Output

Write three files to `listing-audits/nsw/reports/`. Never overwrite an earlier report; each run gets its own dated files.

### `lns-compliance-report-YYYY-MM-DD.md`
1. **Legal frame as read today** — the Step 1 table (source, URL, date read, stage status).
2. **TL;DR** — 3–5 bullets: overall health, biggest risk, top 3 actions.
3. **Health score** — listings audited; % clean; counts by severity; counts by check ID.
4. **Priority fixes** — every High finding, grouped by listing.
5. **Readiness** — all P2 listings in one table (address, agent, current price display) so they can be priced before commencement.
6. **ESP cross-check list** — priced listings not on the Listing Register (address, agent, advertised price), for the agents to confirm against the agency agreement.
7. **Full findings** — table: Address | Agent | Check ID | Severity | Confidence | Field/channel | Exact quote | Why it's an issue | Suggested fix.
8. **Limits** — what couldn't be verified and why.

### `lns-compliance-findings-YYYY-MM-DD.csv`
One row per finding, same columns as the full findings table, plus `notion_url` and `listing_link`.

### `lns-listing-checks-YYYY-MM-DD.csv`
One row per audited listing, with columns matching the Notion **Listing Checks** database so a person can import it after review:
`Check` (address + date) | `Check date` | `Office` (LNS) | `Listing` (address) | `Where checked` (Atlas website) | `Advertised price` | `On Listing Register` | `Price at or above ESP` | `Price shown (Stage 2)` | `No offers over or +` | `Comparables within window` | `Result` | `Finding` | `Checked by` (Claude (automated))

- Checkboxes: `Yes` / `No` / blank if unknown.
- `Result`: **Pass** = no findings; **Minor** = only Medium, Readiness or Best practice findings; **Needs Alice to check** = any High or Needs evidence finding. **Never use "Breach"** — that is a licensee decision.
- `Finding`: one line listing the check IDs and the main quote.

**Suggested fixes:** rewrite the problem phrase only, in plain Australian English. Don't rewrite whole listings.

---

## Step 7 — Self-QA before finishing

- Every finding has an exact quote and a check ID.
- No finding claims underquoting has occurred.
- Every in-scope LNS listing appears in the report (as clean or with findings). Report the count.
- Dates compared against today, not the listing date.
- The legal frame table shows today's read date for each official page, or says which page would not open.
- Every finding on a live listing quotes the website (via Playwright), not only Notion.
- No `Result` is "Breach".
- No Notion writes were made.
