# Backlog age

> **Is work piling up faster than we can handle it?**

Age distribution of unreviewed items by queue.

| | |
|---|---|
| Area | Operations |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Daily snapshot |
| Owner | Workforce management |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`now − first_seen_at for every open item, shown as a distribution per queue`

## Why it matters

Your early warning for a capacity problem.

## Watch out

Queue size alone misleads. One old severe item outweighs many new minor ones.

**Read it with:** [SLA attainment](sla-attainment.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Snapshot every open item in every queue at the same time each day.
2. Bucket by age: under 1 day, 1 to 7 days, over 7 days (add an under-1-hour bucket for tier 1).
3. Weight by severity. One tier 1 item aged two days is an incident; 5,000 tier 4 items aged two days may be fine.
4. Chart the oldest bucket over time. It shows a capacity problem weeks before SLAs collapse.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Items older than 7 days grew from 300 to 2,100 in three weeks while total queue size stayed flat. Reviewers were picking the easy items first.

### No data team yet?

Each morning, note the age of the oldest item in each queue.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT queue, severity,
  COUNT(*) FILTER (WHERE NOW() - first_seen_at < INTERVAL '1 day') AS under_1d,
  COUNT(*) FILTER (WHERE NOW() - first_seen_at
                   BETWEEN INTERVAL '1 day' AND INTERVAL '7 days') AS d1_to_7,
  COUNT(*) FILTER (WHERE NOW() - first_seen_at > INTERVAL '7 days') AS over_7d
FROM open_queue_items
GROUP BY queue, severity;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
