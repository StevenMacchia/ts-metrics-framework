# Consistency across languages and markets

> **Are we as accurate in every language as we are in our best one?**

QA agreement and overturn rates broken out by language and region.

| | |
|---|---|
| Area | Decision quality |
| Tier | Diagnostic |
| Good direction | Higher is better |
| How often | Quarterly |
| Owner | Quality team, with regional policy leads |
| Platforms | All platforms |
| Program stage | Mature and later |

## The formula

`QA agreement and overturn rate per language, compared with your best language`

## Why it matters

Enforcement gaps usually show up in languages with the fewest resources first.

## Watch out

Small markets have tiny samples. Pool data by quarter.

**Read it with:** [Appeal overturn rate](appeal-overturn-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Add language and market fields to every QA and appeal record.
2. Make sure each major language has qualified native-speaker experts. Machine-translated QA is not reliable.
3. Pool small languages by quarter until each has at least 100 checks.
4. Chart each language's gap to your best one, and flag anything more than 5 points behind.

**Sample size.** At an expected rate of 90% and a margin of ±4 points (95% confidence), label about 217 random items. Halving the margin needs four times as many.

### What you need to log

- **QA re-reviews** (`qa_reviews`): one row is one decision re-reviewed blind by an expert.
- **Appeals** (`appeals`): one row is one appeal against one decision.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

English agreement is 93%, Arabic 81% and Tagalog 76%. The Tagalog queue is staffed by a vendor without native reviewers.

### No data team yet?

Start by comparing your top two languages only.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT a.language,
  AVG(CASE WHEN q.expert_label = q.original_label THEN 1.0 ELSE 0 END) AS agreement,
  COUNT(*) AS checks
FROM qa_reviews q
JOIN enforcement_actions a ON a.action_id = q.action_id
WHERE q.sampled_at >= DATE_TRUNC('quarter', CURRENT_DATE)
GROUP BY a.language
HAVING COUNT(*) >= 100
ORDER BY agreement;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
