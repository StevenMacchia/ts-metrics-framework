# Time to action by severity (p90)

> **How fast do we act on the most serious problems?**

Time from report or detection to decision, split by severity tier, reported at p50 and p90.

| | |
|---|---|
| Area | Operations |
| Tier | Health |
| Good direction | Lower is better |
| How often | Daily for tier 1, weekly for the rest |
| Owner | T&S operations |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`decided_at − first_seen_at, at p50 and p90 per severity tier`

## Why it matters

Shows whether the most urgent harms are handled fastest.

## Watch out

Averages hide the long tail. The p90 is where incidents come from.

**Read it with:** [QA agreement rate](qa-agreement-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Fix one definition of when the clock starts: the first report or detection on that object, whichever came first.
2. Give every item a severity tier at intake (for example tier 1 for imminent harm and child safety, tier 4 for low-harm spam).
3. Report p50 and p90 per tier. Leadership should always see tier 1 first.
4. Exclude nothing silently. Show auto-closed or merged items as their own line.

### On your platform

- **On a social platform:** Viral content needs action within minutes. Add speed of spread to your severity rules.
- **In a game:** Acting during the match matters most. A mute after the match ends does little for the victim.
- **On a dating app:** Threats of violence and unsafe meetups should be tier 1, with a route to emergency help.
- **In a generative AI product:** Most enforcement is automatic and instant. Measure time to action on user reports and red-team findings.
- **In a fintech or payments product:** For scams, speed is money: every hour before a payment is held lowers the chance of getting it back.
- **On a gig, delivery or rental platform:** Safety reports during a live trip are tier 1 and need a real-time route, including emergency services.
- **On a product for kids or education:** Contact that looks like grooming is tier 1, with its own on-call route to child-safety specialists.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Tier 1 p50 is 12 minutes but p90 is 6 hours, because overnight reports wait for the morning shift. The fix is on-call coverage, not more reviewers.

### No data team yet?

Your ticketing tool already stores created and closed times. Export them and use PERCENTILE in a spreadsheet.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT severity,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY decided_at - first_seen_at) AS p50,
  PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY decided_at - first_seen_at) AS p90
FROM enforcement_actions
WHERE decided_at >= CURRENT_DATE - INTERVAL '7 days'
GROUP BY severity
ORDER BY severity;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
