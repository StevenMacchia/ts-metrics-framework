# Toxicity per 1,000 match-hours

> **How often do players run into abuse while they play?**

Confirmed toxic incidents (text and voice) per 1,000 hours of play.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Weekly, and daily during launches and events |
| Owner | Player safety, with game analytics |
| Platforms | Gaming |
| Program stage | Early and later |

## The formula

`confirmed toxic incidents ÷ match-hours × 1,000`

## Why it matters

Normalizes for engagement growth, so a busy launch week doesn't look like a spike.

## Watch out

Voice is under-reported. Measure it separately if you can.

**Read it with:** [Churn after toxic exposure](churn-after-toxic-exposure.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Count incidents confirmed by review or by a high-precision classifier, not raw reports.
2. Take match-hours from game telemetry for the same period, modes and regions.
3. Break it out by channel (text, voice, gameplay griefing) and by mode. Ranked and casual play behave differently.
4. Measure voice from its own labeled sample of sessions (where players have consented), because players rarely report voice abuse.

### On your platform

- **In a game:** Use match-hours in modes where players can talk to strangers. Solo modes dilute the rate.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **User reports** (`user_reports`): one row is one report filed by one user.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

1,200 confirmed incidents over 400,000 match-hours is 3 per 1,000 hours. A new mode launching at 7 is a clear signal before reports pile up.

### No data team yet?

Each month, divide confirmed reports by the hours played shown in your game analytics dashboard.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
-- daily_mode_stats: one row per mode per day,
-- joining telemetry hours to confirmed incidents
SELECT mode,
  1000.0 * SUM(confirmed_incidents) / SUM(match_hours) AS incidents_per_1k_hours
FROM daily_mode_stats
WHERE day >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY mode;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
