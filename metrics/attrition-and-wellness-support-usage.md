# Attrition and wellness-support usage

> **Are we burning out the people who keep users safe?**

Reviewer attrition rate plus uptake of counseling and wellness programs.

| | |
|---|---|
| Area | People |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Monthly, with a quarterly survey |
| Owner | T&S leadership, with HR |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`monthly leavers ÷ average headcount × 12; support sessions per person per month`

## Why it matters

Attrition destroys expertise and QA scores, and it is expensive.

## Watch out

Low support uptake can mean stigma, not good health. Ask anonymously.

**Read it with:** [Graphic-exposure hours per reviewer](graphic-exposure-hours-per-reviewer.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Get monthly headcount and leavers from HR and from each vendor, split by queue exposure level.
2. Annualize it: monthly leavers ÷ average headcount × 12.
3. Get support usage from your wellness provider as anonymous, aggregated counts only.
4. Run an anonymous pulse survey every quarter. It explains what the numbers cannot.

### What you need to log

- **Workforce and cost** (`reviewer_weeks`): one row is one reviewer-week, plus monthly cost and headcount rollups.

### Worked example

High-exposure queues lose 4 people a month from an average of 60: 80% annualized attrition, against 25% elsewhere. Each leaver takes months of training with them.

### No data team yet?

Track leavers per quarter against headcount, and ask your vendors for the same figures.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT month, exposure_level,
  12.0 * leavers / NULLIF(avg_headcount, 0) AS annualized_attrition,
  support_sessions * 1.0 / NULLIF(avg_headcount, 0) AS sessions_per_person
FROM monthly_workforce  -- aggregated; no individual-level data
ORDER BY month DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
