# Prohibited-listing prevalence

> **How much of what's for sale shouldn't be?**

Share of live listings that are counterfeit, prohibited or misrepresented, by category.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | Health |
| Good direction | Lower is better |
| How often | Weekly sample, monthly report |
| Owner | Marketplace integrity, with category managers |
| Platforms | Marketplace |
| Program stage | Scaling and later |

## The formula

`violating listings in a random sample of live listings ÷ listings sampled, per category`

## Why it matters

Shows where buyers are most exposed.

## Watch out

Varies heavily by category. A blended number hides your worst category.

**Read it with:** [Precision and recall by policy area](precision-and-recall-by-policy-area.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Each week, sample live listings at random, stratified by category so small high-risk categories get enough samples.
2. Have trained reviewers label each one compliant, prohibited, counterfeit or misrepresented. Use brand-authentication help for counterfeits.
3. Weight each category by its share of listing views to get a buyer-exposure figure.
4. Report your worst three categories on their own, not just the blended number.

**Sample size.** At an expected rate of 1% and a margin of ±0.3 points (95% confidence), label about 4,226 random items. Halving the margin needs four times as many.

### On your platform

- **On a marketplace:** Split by seller age as well as category. New sellers often account for a large share of prohibited listings.

### What you need to log

- **Random exposure sample** (`exposure_sample`): one row is one randomly sampled view (or live listing), labeled by a trained reviewer.
- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

Overall prevalence is 0.8%, but luxury handbags sit at 6%. That category gets authentication requirements; the rest of the catalog does not need them.

### No data team yet?

Every Friday, review 50 random live listings in your riskiest category.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT category,
  AVG(CASE WHEN label <> 'compliant' THEN 1.0 ELSE 0 END) AS prohibited_share,
  COUNT(*) AS sampled
FROM listing_sample
WHERE sampled_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY category
ORDER BY prohibited_share DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
