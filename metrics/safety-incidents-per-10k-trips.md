# Safety incidents per 10k trips

> **How often is someone hurt or put in danger during a trip or booking?**

Confirmed safety incidents (assault, harassment, dangerous driving, property damage) per 10,000 completed trips, bookings or deliveries, by severity.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Weekly for critical incidents, monthly for rates |
| Owner | Safety team, with legal and insurance |
| Platforms | Gig, delivery & rentals |
| Program stage | Early and later |

## The formula

`confirmed safety incidents ÷ completed trips × 10,000, per severity level`

## Why it matters

On gig and rental platforms the harm happens offline, and one serious incident can end up in the news or in court.

## Watch out

Serious incidents are rare, so monthly rates jump around. Show counts by severity and use rolling 12-month rates for the worst categories.

**Read it with:** [Verified-profile share](verified-profile-share.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Agree a severity scale with legal and insurance, for example critical (sexual assault, serious injury), high (physical fight, threats), medium (harassment, dangerous driving), low (rudeness, minor damage).
2. Collect reports from every channel (in-app safety button, support, emergency line, police requests, insurance claims) and merge them into one case per incident.
3. Divide by completed trips, bookings or deliveries in the same period, and report each severity level separately.
4. Count incidents against workers as well as customers. Workers are harmed too, and report less.

### On your platform

- **On a gig, delivery or rental platform:** Record where incidents happen: at pickup, during the trip or stay, or afterwards when people contact each other off the platform.

### What you need to log

- **User reports** (`user_reports`): one row is one report filed by one user.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

Last month had 3 critical and 41 medium incidents across 2.4 million trips: 0.0125 and 0.17 per 10,000. Each critical case gets a full review; the medium rate is the one to trend.

### No data team yet?

Tag every safety ticket with a severity, and count them monthly against completed trips.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT i.severity,
  10000.0 * COUNT(DISTINCT i.incident_id) / MAX(t.completed_trips) AS per_10k_trips
FROM safety_incidents i
JOIN monthly_trips t ON t.month = DATE_TRUNC('month', i.occurred_at)
WHERE i.confirmed
  AND i.occurred_at >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '1 month')
  AND i.occurred_at <  DATE_TRUNC('month', CURRENT_DATE)
GROUP BY i.severity;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
