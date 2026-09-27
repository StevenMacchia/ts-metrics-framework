# Romance-scam reports per 10k matches

> **How often are our users targeted by romance scammers?**

Confirmed scam reports normalized by match volume.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Weekly |
| Owner | Dating safety team |
| Platforms | Dating |
| Program stage | Early and later |

## The formula

`confirmed romance-scam cases ÷ matches × 10,000`

## Why it matters

Scams are the defining harm on dating platforms, and the losses are severe.

## Watch out

Victims under-report out of embarrassment. Add a proactive-detection view.

**Read it with:** [Proactive detection rate](proactive-detection-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Count only cases confirmed by review, and count scammer accounts too, so one scammer with 40 victims is visible.
2. Take matches from product analytics for the same period and region.
3. Show cases caught proactively, before any victim reported, as a separate line.
4. Track the time from first message to the first request for money or to move off the platform. That is the pattern to detect early.

### On your platform

- **On a dating app:** Look for the pattern: fast emotional escalation, then a push to move to another app, then talk of money or crypto investing.

### What you need to log

- **User reports** (`user_reports`): one row is one report filed by one user.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

84 confirmed cases across 300,000 matches is 2.8 per 10,000. If 60% were caught before a report, show both numbers.

### No data team yet?

Tag every confirmed scam case in your ticketing tool, and divide the monthly count by monthly matches.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT week, region,
  10000.0 * SUM(confirmed_scam_cases) / SUM(matches) AS cases_per_10k_matches,
  SUM(proactive_scam_cases) AS caught_before_report
FROM weekly_region_stats
GROUP BY week, region
ORDER BY week DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
