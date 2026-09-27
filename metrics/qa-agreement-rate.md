# QA agreement rate

> **Do our reviewers make the same call an expert would?**

Agreement between front-line decisions and expert re-review on a random sample.

| | |
|---|---|
| Area | Decision quality |
| Tier | Health |
| Good direction | Higher is better |
| How often | Weekly |
| Owner | Quality team |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`sampled decisions where the expert agrees ÷ decisions sampled`

## Why it matters

Your ground truth for reviewer and vendor quality.

## Watch out

Experts also disagree. Measure expert-to-expert agreement first.

**Read it with:** [SLA attainment](sla-attainment.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Each week, draw a random sample of decisions per queue and vendor, sized so each reviewer gets 20 to 30 checks a month.
2. Have experts re-label blind, without seeing the original decision or who made it.
3. Calibrate the experts first: have them label the same set and measure how often they agree with each other. That is your ceiling.
4. Report agreement per queue, vendor and policy area, and walk through the disagreements in a weekly calibration session.

**Sample size.** At an expected rate of 90% and a margin of ±3 points (95% confidence), label about 385 random items. Halving the margin needs four times as many.

### What you need to log

- **QA re-reviews** (`qa_reviews`): one row is one decision re-reviewed blind by an expert.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Vendor A agrees with experts 94% of the time and Vendor B 86%. Experts agree with each other 95%, so A is near the ceiling and B has a training gap.

### No data team yet?

A lead re-reviews 10 random decisions per reviewer each week in a shared sheet.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT a.vendor, a.queue,
  AVG(CASE WHEN q.expert_label = q.original_label THEN 1.0 ELSE 0 END) AS agreement,
  COUNT(*) AS checks
FROM qa_reviews q
JOIN enforcement_actions a ON a.action_id = q.action_id
WHERE q.sampled_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY a.vendor, a.queue;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
