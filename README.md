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
2. Cursor Settings → MCP → make sure **Notion** is connected with access to *Atlas iQ App → Property Marketing Database*.

**Each run**
1. Pull the latest (`git pull`).
2. Open Agent chat (Cmd+I) and paste:

   > Follow @listing-audits/nsw/lns-listing-audit.md exactly. Start with Step 0 (calibration) and stop after it. Show me the per-fixture results, recall and false positives before auditing any live listings.

3. Review the calibration result. If it caught every High finding and flagged nothing High/Medium on fixture F8, reply:

   > Calibration accepted. Continue with Steps 1–6 for all LNS listings.

4. Check the two new files in `listing-audits/nsw/reports/`, then commit them:
   `git add listing-audits/nsw/reports && git commit -m "LNS audit YYYY-MM-DD" && git push`
5. Send the report to the Licensee-in-Charge.

**Suggested cadence:** fortnightly, plus once the NSW price-guide rule commences (expected November 2026).

---

## Before every run, check

- **Legal frame is current.** Step 2 of the brief reflects NSW law as at October 2026. When NSW Fair Trading announces the commencement date for the underquoting reforms, update Step 2 and P2 severity (Readiness → High).
- **Data is fresh.** The Properties database updates daily at 10am from atlas.com.au.

## Extending to other offices

Copy `lns-listing-audit.md`, change the `Office` filter (STH, PRJ, etc.) and rename. Byron Bay (BYB) and Brisbane need their own legal frame — NSW and QLD rules differ.

## Changing a brief

Edit it here, commit with a clear message, and note what changed. Re-run calibration after any change to the checks.
