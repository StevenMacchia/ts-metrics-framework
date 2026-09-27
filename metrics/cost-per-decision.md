# Cost per decision

> **What does each moderation decision cost us?**

Fully loaded review cost divided by decisions, by queue and vendor.

| | |
|---|---|
| Area | Operations |
| Tier | Diagnostic |
| Good direction | Keep it in a healthy range |
| How often | Monthly |
| Owner | T&S operations, with Finance |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`fully loaded review cost ÷ decisions made, per queue and vendor`

## Why it matters

Needed to defend budget and compare vendors.

## Watch out

Optimizing it alone lowers quality. Always show it next to QA agreement.

**Read it with:** [QA agreement rate](qa-agreement-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Collect the monthly cost per queue: vendor invoices, internal salaries and benefits, tooling and licences, QA and management overhead.
2. Count human decisions per queue for the same month.
3. Show human-only and blended (human plus automated) cost per decision separately.
4. Put it next to QA agreement on every slide, so nobody optimizes cost on its own.

### On your platform

- **In a generative AI product:** Include the compute cost of classifiers when you compare automated and human review.

### What you need to log

- **Workforce and cost** (`reviewer_weeks`): one row is one reviewer-week, plus monthly cost and headcount rollups.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Vendor B costs $0.38 a decision against $0.52 for Vendor A, but agrees with experts 8 points less often. Rework and appeals make B the more expensive choice.

### No data team yet?

Divide each monthly vendor invoice by the number of decisions it covered.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT c.month, c.queue, c.vendor,
  c.total_cost / NULLIF(d.decisions, 0) AS cost_per_decision
FROM monthly_queue_costs c
JOIN (SELECT DATE_TRUNC('month', decided_at) AS month, queue, vendor,
        COUNT(*) AS decisions
      FROM enforcement_actions
      WHERE source <> 'automated'
      GROUP BY 1, 2, 3) d
  ON d.month = c.month AND d.queue = c.queue AND d.vendor = c.vendor;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
