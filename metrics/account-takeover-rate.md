# Account takeover rate

> **How often do attackers take over our users' accounts?**

Confirmed account takeovers per 10,000 active accounts, with the money or data lost.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Weekly, reported monthly |
| Owner | Account security or fraud team |
| Platforms | Fintech & payments, Marketplace, Gaming |
| Program stage | Early and later |

## The formula

`confirmed account takeovers ÷ active accounts × 10,000`

## Why it matters

Takeovers hurt your most loyal users, and the losses land on you or on them.

## Watch out

Victims report late or blame themselves. Count cases found by detection, not just reports.

**Read it with:** [Fraud loss rate](fraud-loss-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Define a confirmed takeover: someone other than the owner got in and acted (changed credentials, moved money, messaged contacts), confirmed by the owner or an investigation.
2. Link each case to the security events before it: new device, password reset, 2-step verification change, new payee.
3. Count per 10,000 active accounts, split by how the attacker got in: phishing, reused passwords, SIM swap or session theft.
4. Record the loss per case and the time from takeover to lock. That shows whether detection is fast enough.

### On your platform

- **In a fintech or payments product:** Include cases where the customer was tricked into sharing a code. Whether or not you reimburse them, they are takeovers.
- **On a marketplace:** Seller takeovers are the costly ones: attackers change the payout bank account. Alert on payout changes after a new-device login.
- **In a game:** Stolen accounts are resold for their items. Watch for a new device followed by mass item trading.

### What you need to log

- **Login and security events** (`auth_events`): one row is one login, password reset, device change or 2-step verification event.
- **User reports** (`user_reports`): one row is one report filed by one user.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

42 confirmed takeovers across 150,000 active accounts is 2.8 per 10,000. 30 came through SIM swaps, so text-message codes are the weak point.

### No data team yet?

Tag every support ticket where a user says "someone got into my account", and confirm them weekly.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
WITH ato AS (
  SELECT DATE_TRUNC('month', confirmed_at) AS month, entry_method,
         COUNT(*) AS takeovers, SUM(loss_amount) AS losses
  FROM account_takeovers
  GROUP BY 1, 2)
SELECT ato.month, ato.entry_method,
  10000.0 * ato.takeovers / u.active_accounts AS ato_per_10k,
  ato.losses
FROM ato
JOIN monthly_active u ON u.month = ato.month;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
