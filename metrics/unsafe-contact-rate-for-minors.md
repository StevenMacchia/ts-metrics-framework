# Unsafe-contact rate for minors

> **How often do adults try to contact children inappropriately on our product?**

Confirmed cases of adults seeking inappropriate contact with minors (grooming, sexual solicitation, sextortion) per 10,000 young users.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Weekly with specialists, monthly to leadership |
| Owner | Child-safety team |
| Platforms | Kids & education, Social / user-generated content, Gaming |
| Program stage | Early and later |

## The formula

`confirmed unsafe adult-to-minor contact cases ÷ active young users × 10,000`

## Why it matters

The harm with the worst consequences on any product children use, and the first thing regulators check.

## Watch out

Children rarely report it themselves. Reports alone will badly undercount it.

**Read it with:** [Age-assurance coverage](age-assurance-coverage.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Define the case types with your child-safety specialists: grooming, sexual solicitation, sextortion, and adults pushing a child to move to another app.
2. Count confirmed cases by adult account, and record how each was found: the child, a parent or teacher, or detection.
3. Divide by active users under 18 (or your own age bands) for the same period.
4. Record where contact started (search, recommendations, open messages, voice, friend requests) so you can close the path, not just ban the account.
5. Cases involving apparent child sexual exploitation must be reported to the authorities (in the US, the NCMEC CyberTipline). Track that reporting with its own timestamps.

### On your platform

- **On a product for kids or education:** Measure across every way adults and children can meet: chat, voice, friend requests, comments and classroom tools.
- **On a social platform:** You need a reliable age signal to count young users. Read it with age-assurance coverage.
- **In a game:** Voice chat and friend requests from strangers are common starting points. Split the rate by entry path.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **User reports** (`user_reports`): one row is one report filed by one user.
- **Automated detections** (`detections`): one row is one flag from a classifier, hash match or rule.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

18 confirmed cases among 90,000 young users is 2 per 10,000. 12 began with open direct messages from accounts the child didn't follow, so the fix is a default setting, not more moderators.

### No data team yet?

Give every child-safety ticket a case type and a "how we found it" tag, and count them monthly with a specialist.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT DATE_TRUNC('month', c.confirmed_at) AS month, c.found_by,
  10000.0 * COUNT(DISTINCT c.case_id) / MAX(u.young_users) AS cases_per_10k
FROM child_safety_cases c
JOIN monthly_young_users u
  ON u.month = DATE_TRUNC('month', c.confirmed_at)
GROUP BY 1, 2
ORDER BY 1 DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
