# Statement-of-reasons coverage

> **When we restrict someone, do we properly tell them why?**

Share of restrictive actions that come with a compliant explanation to the user.

| | |
|---|---|
| Area | Compliance |
| Tier | Health |
| Good direction | Higher is better |
| How often | Monthly |
| Owner | Compliance, with T&S operations |
| Platforms | All platforms |
| Program stage | Early and later · where the EU DSA or UK OSA applies |

## The formula

`restrictive actions with a compliant statement of reasons ÷ restrictive actions`

## Why it matters

A core obligation under the EU Digital Services Act and a common audit finding.

## Watch out

Templated reasons that don't name the specific policy don't count.

**Read it with:** [Appeal overturn rate](appeal-overturn-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. List which actions are restrictive: removal, demotion or reduced visibility, demonetization, suspension and termination.
2. Log whether a statement was sent for each one, and whether it named the specific policy or law, the facts, any use of automation, and how to appeal.
3. Each month, sample 100 statements and check them against that list, not just whether one was sent.
4. If you are an online platform under the DSA, reconcile your counts with what you submit to the DSA Transparency Database.

### On your platform

- **On a social platform:** Demotions and reduced visibility need statements too. That is where most teams fall short.
- **On a marketplace:** Listing removals, seller restrictions and held payments all need statements.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Legal notices and risk register** (`legal_notices`): one row is one legal notice, or one required risk assessment with its mitigations.

### Worked example

Statements go out for 99% of removals but only 40% of demotions, because demotions were never wired to the notice system. That is an audit finding waiting to happen.

### No data team yet?

Each month, audit 25 enforcement notices against a four-point checklist.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT action,
  AVG(CASE WHEN sor_sent AND sor_names_policy AND sor_has_appeal_info
      THEN 1.0 ELSE 0 END) AS compliant_coverage
FROM enforcement_actions
WHERE action IN ('remove', 'demote', 'demonetize', 'suspend', 'terminate')
  AND decided_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY action;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
