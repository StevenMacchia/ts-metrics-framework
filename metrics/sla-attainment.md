# SLA attainment

> **Do we hit the response times we promised?**

Share of items decided within the target time for their tier.

| | |
|---|---|
| Area | Operations |
| Tier | Health |
| Good direction | Higher is better |
| How often | Weekly |
| Owner | T&S operations and vendor management |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`items decided within their tier's target ÷ items due`

## Why it matters

The operational commitment you report upward.

## Watch out

Teams hit SLAs by rushing. Watch QA agreement at the same time.

**Read it with:** [QA agreement rate](qa-agreement-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Write down a target per tier (for example tier 1 within 1 hour, tier 2 within 24 hours) and store it as a table, not in people's heads.
2. Join every decision to its target and mark it met or missed.
3. Count items still open past their target as misses. Otherwise a growing backlog makes attainment look better.
4. Report per tier, queue and vendor.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Tier 2 attainment is 97% on decided items, but 1,400 items are open past target. Counted properly, it is 88%.

### No data team yet?

Set a target for your most severe category first, and count the misses each week.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT a.severity, a.vendor,
  AVG(CASE WHEN a.decided_at - a.first_seen_at <= s.target THEN 1.0 ELSE 0 END)
    AS sla_attainment
FROM enforcement_actions a
JOIN sla_targets s ON s.severity = a.severity
WHERE a.decided_at >= CURRENT_DATE - INTERVAL '7 days'
GROUP BY a.severity, a.vendor;
-- Then add open items already past target as misses
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
