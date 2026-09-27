# Churn after toxic exposure

> **Do people quit after they've been abused?**

Retention gap between users who experienced a confirmed incident and matched users who didn't.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Quarterly |
| Owner | Data science |
| Platforms | Gaming, Social / user-generated content, Dating, Kids & education |
| Program stage | Mature and later |

## The formula

`30-day retention of matched unexposed users − 30-day retention of exposed users`

## Why it matters

The most persuasive metric for executives: safety is retention.

## Watch out

This is correlation. Use matched cohorts before you claim causation.

**Read it with:** [Toxicity per 1,000 match-hours](toxicity-per-1-000-match-hours.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Define exposure: the user was the target of a confirmed incident, not only someone who reported one.
2. For each exposed user, pick an unexposed user with similar tenure, activity, region and device (a matched cohort).
3. Compare the share of each group still active 30 days later.
4. Repeat every quarter. Present it as a range, and call it an association unless you have run a proper causal analysis.

### On your platform

- **In a game:** Use days-played retention, and match players on skill rank. Ranked players are targeted and churn differently.
- **On a social platform:** Use 30-day active retention, and match on audience size. Larger accounts are targeted more and churn differently.
- **On a dating app:** Focus on users harassed in their first week. That's when a bad experience makes people delete the app.
- **On a product for kids or education:** Involve your privacy team, report only aggregates, and include parents who close accounts after an incident.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

Exposed players retain at 52% after 30 days against 61% for matched players. A 9-point gap across 20,000 exposed players a month is a number a CFO understands.

### No data team yet?

Compare the 30-day retention of players who filed a harassment report with everyone else. Rough, but directional.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT cohort,  -- 'exposed' or 'matched_control'
  AVG(CASE WHEN active_day_30 THEN 1.0 ELSE 0 END) AS retention_30d,
  COUNT(*) AS users
FROM exposure_cohorts
WHERE cohort_month = DATE_TRUNC('month', CURRENT_DATE - INTERVAL '2 months')
GROUP BY cohort;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
