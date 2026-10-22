# Atlas Compliance

Home for all Atlas compliance tooling: agent briefs, test fixtures and audit reports. Keep compliance work here, separate from app, brand and marketing repos.

**Owner:** Theo Adricula (Head of Marketing & Technology)
**Handover:** Alice (marketing ops) — runs audits and circulates reports
**Sign-off on findings:** Licensee-in-Charge for the office audited

> Reports are a triage list, not legal advice. Every finding needs a licensee's decision before anything changes on a listing.

---

## What's here

```
compliance/
├── AGENTS.md                          Rules every agent in this repo must follow
├── README.md                          This file
└── listing-audits/
    └── nsw/
        ├── lns-listing-audit.md       Agent brief: NSW advertising compliance, Lower North Shore
        ├── calibration-fixtures.json  Synthetic listings with known answers (accuracy test)
        └── reports/                   Dated audit outputs (.md report + .csv findings)
```

---

## Running the LNS listing audit (Cursor)

**One-time setup**
1. Clone this repo and open it in Cursor.
2. Cursor Settings → MCP → make sure **Notion** is connected with access to *Atlas iQ App → Property Marketing Database* and the *COMPLIANCE* page.
3. Make sure **Playwright** is available to the agent. It reads the live atlas.com.au listings and the official NSW Fair Trading pages, as set out in Notion → *Compliance monitoring — handover for Alice*.

**Each run**
1. Pull the latest (`git pull`).
2. Open Agent chat (Cmd+I) and paste:

   > Follow @listing-audits/nsw/lns-listing-audit.md exactly. Start with Step 0 (calibration) and stop after it. Show me the per-fixture results, recall and false positives before auditing any live listings.

3. Review the calibration result. If it caught every High finding and flagged nothing High/Medium on fixture F8, reply:

   > Calibration accepted. Continue with Steps 1–7 for all LNS listings.

4. Check the three new files in `listing-audits/nsw/reports/`, then commit them:
   `git add listing-audits/nsw/reports && git commit -m "LNS audit YYYY-MM-DD" && git push`
5. After review, import `lns-listing-checks-*.csv` into Notion → COMPLIANCE → **Listing Checks**, and log anything confirmed as a breach in the **Non-Compliance Register** with LIC sign-off.
6. Send the report to the Licensee-in-Charge.

**Cadence:** every second Wednesday from 4 November 2026, matching the Listing Checks automation in the COMPLIANCE page.

**How this fits the Notion COMPLIANCE system:** this repo holds the agent and its reports. Notion stays the system of record (Listing Register, Listing Checks, Non-Compliance Register). The agent reads Notion and never writes to it; a person imports the reviewed results.

---

## Before every run, check

- **Legal frame.** The agent re-reads the official Fair Trading pages each run (Step 1) and reports what they say. If it reports a conflict with the baseline in Step 3, update the brief before relying on the results.
- **Data is fresh.** The Properties database updates daily at 10am from atlas.com.au.

## Extending to other offices

Copy `lns-listing-audit.md`, change the `Office` filter (STH, PRJ, etc.) and rename. Byron Bay (BYB) and Brisbane need their own legal frame — NSW and QLD rules differ.

## Changing a brief

Edit it here, commit with a clear message, and note what changed. Re-run calibration after any change to the checks.
