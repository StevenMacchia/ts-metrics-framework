# Appeal overturn rate

> **When someone appeals, how often were we wrong?**

Share of appealed decisions that are reversed, by policy area and enforcement source.

| | |
|---|---|
| Area | Decision quality |
| Tier | Health |
| Good direction | Lower is better |
| How often | Monthly |
| Owner | Quality team, independent of operations |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`appeals that reversed the decision ÷ appeals decided`

## Why it matters

The clearest public signal of over-enforcement.

## Watch out

Few users appeal. A low rate with low appeal volume can hide errors.

**Read it with:** [Proactive detection rate](proactive-detection-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Join each appeal to its original action, so you know the policy, source (automated or human), reviewer and vendor.
2. Count only decided appeals, and report overturns per policy area and per source.
3. Track the appeal rate too (appeals ÷ actions). A low overturn rate means little if almost nobody appeals.
4. Send every overturn back to the quality team as a training case.

### On your platform

- **On a marketplace:** Seller appeals often include evidence such as invoices or authenticity certificates. Track which evidence leads to overturns.
- **In a generative AI product:** Most users can't appeal a refusal. If you add a "this shouldn't have been blocked" button, treat it as an appeal.
- **In a fintech or payments product:** Appeals are often complaints about frozen accounts or held payments. Measure how fast they are resolved, not only the outcome.

### What you need to log

- **Appeals** (`appeals`): one row is one appeal against one decision.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Automated nudity removals are overturned 22% of the time on appeal against 4% for human removals. That classifier's threshold is too aggressive.

### No data team yet?

Tag appeal tickets upheld or overturned, and count them each month.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT a.policy, a.source,
  SUM(CASE WHEN ap.outcome = 'overturned' THEN 1 ELSE 0 END) * 1.0
    / NULLIF(COUNT(ap.appeal_id), 0) AS overturn_rate,
  COUNT(ap.appeal_id) * 1.0 / COUNT(*) AS appeal_rate
FROM enforcement_actions a
LEFT JOIN appeals ap
  ON ap.action_id = a.action_id AND ap.resolved_at IS NOT NULL
WHERE a.decided_at >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY a.policy, a.source;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
