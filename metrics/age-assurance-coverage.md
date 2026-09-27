# Age-assurance coverage

> **Do we actually know which of our users are children?**

Share of active users whose age was checked by something stronger than a typed date of birth, and how many under-age users were found.

| | |
|---|---|
| Area | Detection |
| Tier | Health |
| Good direction | Higher is better |
| How often | Monthly |
| Owner | T&S, with identity and product |
| Platforms | Kids & education, Social / user-generated content, Gaming, Dating |
| Program stage | Scaling and later |

## The formula

`active users with an age check stronger than self-declaration ÷ active users`

## Why it matters

Every child-safety protection depends on knowing who is a child. Regulators such as Ofcom now expect highly effective age checks where children could see the most harmful content.

## Watch out

Self-declared age is not age assurance. Counting it inflates coverage.

**Read it with:** [Unsafe-contact rate for minors](unsafe-contact-rate-for-minors.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. List the age signals you use and rank them by strength: typed date of birth, age estimation (face or behavior), ID document, parent or school verification.
2. Store the strongest method and its date on each account, and count only active accounts.
3. Report coverage per method, and how many under-age accounts were found and removed or moved to a teen experience.
4. Sample accounts marked as adults and check how often the method was wrong.

### On your platform

- **On a product for kids or education:** Also measure the reverse: adults pretending to be children, which is how many grooming cases begin.
- **On a social platform:** Report coverage separately for surfaces where children could see the most harmful content.
- **In a game:** Voice and open chat often depend on age. Report coverage for players who use them.
- **On a dating app:** Your aim is to keep minors out entirely. Report how many under-18 accounts you found and how.

### What you need to log

- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Only 29% of active users have an age check stronger than a typed date of birth. Adding age estimation at sign-up raises that to 64% and finds 3,100 likely under-13 accounts in the first month.

### No data team yet?

Count how many active accounts have anything stronger than a typed date of birth. That is your starting point.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT age_method,  -- 'self_declared', 'estimation', 'id_document', 'parental'
  COUNT(*) * 1.0 / SUM(COUNT(*)) OVER () AS share_of_active
FROM accounts
WHERE last_active_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY age_method;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
