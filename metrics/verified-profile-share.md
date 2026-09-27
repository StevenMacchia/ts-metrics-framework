# Verified-profile share

> **How many of our active users have proven who they are?**

Share of active profiles or sellers that passed identity or liveness checks.

| | |
|---|---|
| Area | Detection |
| Tier | Health |
| Good direction | Higher is better |
| How often | Monthly |
| Owner | Identity or integrity team |
| Platforms | Dating, Marketplace, Fintech & payments, Gig, delivery & rentals |
| Program stage | Scaling and later |

## The formula

`active accounts that passed verification ÷ all active accounts`

## Why it matters

A leading indicator for fraud and impersonation.

## Watch out

Verification is not good behavior. Verified bad actors exist.

**Read it with:** [Fraud loss rate](fraud-loss-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Store verification status, method (ID document, selfie liveness, phone, bank) and date on the account record.
2. Count only accounts active in the period, so dormant verified accounts do not inflate the number.
3. Split by method. A phone check and a liveness check are not the same level of assurance.
4. Compare violation rates of verified and unverified accounts, to prove verification is worth its friction.

### On your platform

- **On a dating app:** Selfie or liveness checks matter most. The main risk is fake identity, not payment fraud.
- **On a marketplace:** Verify sellers first, especially above a sales threshold. Verifying buyers adds friction for less benefit.
- **In a fintech or payments product:** This is your KYC pass rate. Report the share verified at onboarding and after risk events, and how many legitimate customers get stuck.
- **On a gig, delivery or rental platform:** Include background checks for workers and real-time selfie checks, which stop banned workers from using a friend's account.

### What you need to log

- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

38% of active sellers are verified, and they account for 9% of fraud cases. That makes the case for requiring verification above a sales threshold.

### No data team yet?

Take the pass count from your verification vendor's dashboard and divide by monthly active users.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT verification_method,  -- includes 'none'
  COUNT(*) * 1.0 / SUM(COUNT(*)) OVER () AS share_of_active
FROM accounts
WHERE last_active_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY verification_method;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
