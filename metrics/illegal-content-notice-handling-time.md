# Illegal-content notice handling time

> **How fast do we respond when someone formally reports illegal content?**

Time from a legal notice (for example a trusted flagger or authority) to decision.

| | |
|---|---|
| Area | Compliance |
| Tier | Health |
| Good direction | Lower is better |
| How often | Weekly |
| Owner | Legal operations, with T&S |
| Platforms | All platforms |
| Program stage | Early and later · where the EU DSA or UK OSA applies |

## The formula

`decided_at − received_at for legal notices, at p50 and p90, per notice source`

## Why it matters

Regulators expect prompt, prioritized handling.

## Watch out

Trusted-flagger notices need their own queue and SLA.

**Read it with:** [Statement-of-reasons coverage](statement-of-reasons-coverage.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Route every legal notice (user notices of illegal content, trusted flaggers, authorities, court orders) into one intake that stamps the time received.
2. Tag the source type, so trusted-flagger and authority notices can be prioritized and measured on their own.
3. Record the decision time, and whether the notifier was told the outcome.
4. Report p50 and p90 per source, and the share handled within your internal target.

### On your platform

- **On a social platform:** Notices spike during elections and crises. Plan extra capacity for trusted flaggers at those times.
- **On a marketplace:** Many notices are brand complaints about counterfeits. Track intellectual-property notices separately from other illegal content.

### What you need to log

- **Legal notices and risk register** (`legal_notices`): one row is one legal notice, or one required risk assessment with its mitigations.

### Worked example

Trusted-flagger notices take 30 hours at p90 because they sit in the general queue. A dedicated queue brings that down to 4.

### No data team yet?

Use a dedicated inbox for legal notices, and log received and answered times in a sheet.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT source_type,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY decided_at - received_at) AS p50,
  PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY decided_at - received_at) AS p90,
  COUNT(*) AS notices
FROM legal_notices
WHERE received_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY source_type;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
