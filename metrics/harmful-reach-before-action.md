# Harmful reach before action

> **How many people see harmful content before we take it down?**

Views a violating item gets before it is actioned (p50 / p90).

| | |
|---|---|
| Area | Harm outcomes |
| Tier | Health |
| Good direction | Lower is better |
| How often | Weekly |
| Owner | T&S operations, with data science |
| Platforms | Social / user-generated content, Gaming, Kids & education |
| Program stage | Scaling and later |

## The formula

`views a violating item received before it was actioned, at p50 and p90`

## Why it matters

Links speed to harm. A fast removal of a viral post matters more than a fast removal of an unseen one.

## Watch out

A handful of viral items dominate. Report the tail, not the mean.

**Read it with:** [Time to action by severity (p90)](time-to-action-by-severity-p90.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Keep a running view counter on every object, and copy it into the decision log (views_at_action) when you act.
2. Limit it to actions that removed or restricted content for a violation.
3. Report the median and 90th percentile per policy area and per source (proactive or user report).
4. Look at the top 1% of items on their own. A few viral items usually account for most of the harmful reach.

### On your platform

- **On a social platform:** Use impressions, not likes or shares. Shares from large accounts drive most of the tail.
- **In a game:** Count players who saw the item: lobby members, stream viewers, or downloads of a player-made map.
- **On a product for kids or education:** Count views by young users separately. The number to lower is child exposure, not total exposure.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

Hate-speech removals have a median of 40 views before action but a p90 of 9,000. The fix is faster triage of fast-growing content, not faster review of everything.

### No data team yet?

When you remove something, write the view count shown on it into the case notes.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT policy,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY views_at_action) AS p50_views,
  PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY views_at_action) AS p90_views,
  SUM(views_at_action) AS total_harmful_views
FROM enforcement_actions
WHERE action IN ('remove', 'restrict')
  AND decided_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY policy;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
