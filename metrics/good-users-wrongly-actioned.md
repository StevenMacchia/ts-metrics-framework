# Good users wrongly actioned

> **How many innocent users do we hurt by mistake?**

Share of active, legitimate users who had content removed, an account restricted or a payment blocked by mistake during the period.

| | |
|---|---|
| Area | Decision quality |
| Tier | Health |
| Good direction | Lower is better |
| How often | Monthly |
| Owner | Quality team, with product analytics |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`legitimate users wrongly actioned ÷ active users`

## Why it matters

Over-enforcement drives away the users you most want to keep, and it rarely shows up on safety dashboards.

## Watch out

Most wrongly actioned users never appeal; they just leave. Estimate it from QA samples, not only from overturned appeals.

**Read it with:** [Proactive detection rate](proactive-detection-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Estimate wrong actions per policy: the error rate from blind QA samples multiplied by the number of actions, plus overturned appeals.
2. Count the unique users affected, not the number of actions.
3. Divide by active users for the same period.
4. Follow what happened to those users next (retention, support contacts, spend) to show the cost of mistakes.

**Sample size.** At an expected rate of 4% and a margin of ±1 points (95% confidence), label about 1,476 random items. Halving the margin needs four times as many.

### On your platform

- **In a fintech or payments product:** Include blocked or held legitimate payments and wrongly frozen accounts. These are the most damaging mistakes.
- **On a marketplace:** Include good sellers whose listings were removed or payouts held by mistake.
- **In a generative AI product:** Count accounts wrongly suspended, alongside over-refusal of individual prompts.
- **In a game:** Include false bans from anti-cheat and chat filters, which often hit your most active players.

### What you need to log

- **QA re-reviews** (`qa_reviews`): one row is one decision re-reviewed blind by an expert.
- **Appeals** (`appeals`): one row is one appeal against one decision.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

QA finds 4% of 250,000 monthly actions are wrong: about 10,000 actions hitting 7,500 users. That is 0.25% of 3 million active users, and they leave at twice the normal rate.

### No data team yet?

Count unique users with an overturned appeal each month. It is a floor, not the full number, but it starts the conversation.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT DATE_TRUNC('month', a.decided_at) AS month,
  COUNT(DISTINCT a.account_id) FILTER (
    WHERE ap.outcome = 'overturned' OR q.expert_label = 'not_violating'
  ) * 1.0 / MAX(u.active_users) AS known_wrong_share
FROM enforcement_actions a
LEFT JOIN appeals ap ON ap.action_id = a.action_id
LEFT JOIN qa_reviews q ON q.action_id = a.action_id
JOIN monthly_active u ON u.month = DATE_TRUNC('month', a.decided_at)
GROUP BY 1;
-- Counts known errors only. Scale up by the QA error rate for unchecked actions.
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
