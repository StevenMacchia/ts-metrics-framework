# Users who feel safe

> **Do our users actually feel safe here?**

Share of surveyed users who say they feel safe on the platform, from a regular in-product survey.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Higher is better |
| How often | Monthly survey, reported quarterly |
| Owner | User research, with T&S |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`respondents answering safe or very safe ÷ all respondents (weighted)`

## Why it matters

It captures harm your logs never see, it is what users tell their friends, and every executive understands it.

## Watch out

Results drift with who answers. Keep the question wording fixed and weight answers to match your user base.

**Read it with:** [Violating-content prevalence](violating-content-prevalence.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Pick one fixed question, such as "How safe do you feel on our app?" on a five-point scale, and never change the wording.
2. Ask a random sample of active users in the product every month, and cap how often any one person is asked.
3. Weight answers to match your user base (region, age band, tenure), and report the share answering safe or very safe with a confidence interval.
4. Split by group: new users, women, younger users, creators. The gaps between groups matter more than the average.

**Sample size.** At an expected rate of 70% and a margin of ±3 points (95% confidence), label about 897 random items. Halving the margin needs four times as many.

### On your platform

- **On a dating app:** Ask after first dates as well as in the app. Offline safety is the main worry.
- **On a gig, delivery or rental platform:** Survey workers and customers separately. Their risks are different.
- **In a generative AI product:** Ask whether users trust the AI's answers to be safe and appropriate for them, not just whether they feel safe.
- **On a product for kids or education:** Ask parents and teachers as well as young users, and use age-appropriate wording.

### What you need to log

- **Safety surveys** (`survey_responses`): one row is one answer to an in-product safety survey.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

74% of users say they feel safe, but only 58% of women aged 18 to 24. That gap, not the average, is the headline for leadership.

### No data team yet?

Add one fixed question to a survey you already run, and track the answer every quarter.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT segment,
  SUM(CASE WHEN answer IN ('safe', 'very_safe') THEN weight ELSE 0 END)
    / SUM(weight) AS feel_safe,
  COUNT(*) AS responses
FROM survey_responses
WHERE question_id = 'feel_safe'
  AND asked_at >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY segment;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
