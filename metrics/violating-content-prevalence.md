# Violating-content prevalence

> **Out of everything people see, how much breaks our rules?**

Share of content views that violate policy, from a statistically valid random sample of impressions.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Daily sample, reported monthly on a trailing 28 days |
| Owner | Data science, with policy for labeling |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`violating views in the sample ÷ all views in the sample`

## Why it matters

Measures exposure: what users actually see, not how busy your team is.

## Watch out

Too-small samples swing month to month. Publish confidence intervals.

**Read it with:** [Appeal overturn rate](appeal-overturn-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Draw a uniform random sample of content views every day (for example 1 in 100,000 impressions). Sample views, not posts.
2. Send each sampled item to trained labelers who apply the current policy blind: two labels per item, with an expert tiebreak.
3. Compute the violating share overall and per policy area, and always report a 95% confidence interval with it.
4. Size the sample so the interval is useful. Near 0.1% prevalence you need about 40,000 labeled views for a ±0.03 point interval.

**Sample size.** At an expected rate of 0.1% and a margin of ±0.03 points (95% confidence), label about 42,642 random items. Halving the margin needs four times as many.

### On your platform

- **On a social platform:** Sample feed, search and recommendation views separately. Recommended content usually carries the most exposure, and ranking changes move it first.
- **On a marketplace:** Sample listing views, not listings, so popular listings count more. Prohibited items cluster in a few categories, so report those on their own.
- **In a game:** Sample chat lines, views of player-made content and, where players have consented, short voice clips. Report text and voice separately.
- **On a dating app:** Sample profile views and first messages. Most harm on dating apps arrives in messages, not on public profiles.
- **In a generative AI product:** Your unit is a generation. Sample production conversations, remove personal data, and label each output together with the prompt that caused it.
- **In a fintech or payments product:** There is little public content. Apply prevalence to what users do see: payee names, payment notes and profile text, where scams and abuse hide.
- **On a gig, delivery or rental platform:** Apply it to listings, profiles and reviews, and to messages between workers and customers.
- **On a product for kids or education:** Sample what young users actually see, separately from adults. The same prevalence in a children's feed is a far bigger problem.

### What you need to log

- **Random exposure sample** (`exposure_sample`): one row is one randomly sampled view (or live listing), labeled by a trained reviewer.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

In 28 days you label 50,000 sampled views and 60 violate. Prevalence is 0.12%, about 12 in every 10,000 views, ±0.03 points.

### No data team yet?

Each week, pull 200 randomly chosen views from your logs and have two people label them in a spreadsheet. Imprecise, but real.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT surface,
  AVG(CASE WHEN label = 'violating' THEN 1.0 ELSE 0 END) AS prevalence,
  COUNT(*) AS sampled_views
FROM exposure_sample
WHERE sampled_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY surface;
-- 95% interval = 1.96 * SQRT(prevalence * (1 - prevalence) / sampled_views)
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
