# Proactive detection rate

> **How much harm do we catch before anyone has to report it?**

Share of actioned violations found by your systems before any user reported them.

| | |
|---|---|
| Area | Detection |
| Tier | Health |
| Good direction | Higher is better |
| How often | Monthly |
| Owner | Detection engineering |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`violations actioned before any user report ÷ all violations actioned`

## Why it matters

Shows how much harm you catch instead of waiting for victims to report it.

## Watch out

It can rise simply because you over-enforce. Always read it with precision.

**Read it with:** [Precision and recall by policy area](precision-and-recall-by-policy-area.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Tag every action with its source: automated detection, proactive human review, or user report.
2. Count an action as proactive only if no user report on that object exists before the action time.
3. Report per policy area. Spam will sit near 100% and harassment much lower, and both can be healthy.
4. Read it next to precision. A rising proactive rate with falling precision means you are catching more innocent content.

### On your platform

- **On a social platform:** Hash matching of known illegal images makes some areas near 100%. Report them separately so they don't hide weak areas.
- **On a marketplace:** Checks run when a listing is created. Count listings blocked before going live as proactive.
- **In a game:** Chat filters that block a message before it's sent count as proactive, but log them separately from removals.
- **On a dating app:** Screening at sign-up (photo checks, device risk) catches scammers before any match. Count those blocks.
- **In a generative AI product:** Input and output classifiers act before the user sees anything, so most enforcement is proactive by design. Precision matters more here.
- **In a fintech or payments product:** Transaction monitoring acts before a payment settles. Count held or blocked payments as proactive.
- **On a gig, delivery or rental platform:** Signals like route changes, unexpected long stops and crash detection are proactive. Count the interventions they trigger.
- **On a product for kids or education:** This matters most here because children rarely report. Track it separately for contact between adults and minors.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Automated detections** (`detections`): one row is one flag from a classifier, hash match or rule.
- **User reports** (`user_reports`): one row is one report filed by one user.

### Worked example

9,300 of 12,000 hate-speech actions happened before any report: a proactive rate of 77.5%.

### No data team yet?

Tag each case "found by us" or "reported by a user" in your ticketing tool.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT a.policy,
  AVG(CASE WHEN NOT EXISTS (
        SELECT 1 FROM user_reports r
        WHERE r.object_id = a.object_id AND r.created_at < a.decided_at)
      THEN 1.0 ELSE 0 END) AS proactive_rate
FROM enforcement_actions a
WHERE a.decided_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY a.policy;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
