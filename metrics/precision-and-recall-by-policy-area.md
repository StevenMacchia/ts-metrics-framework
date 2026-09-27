# Precision and recall by policy area

> **When our systems act, are they right, and how much do they miss?**

For each classifier and rule: how many of its actions were correct, and how much violating content it caught.

| | |
|---|---|
| Area | Detection |
| Tier | Health |
| Good direction | Higher is better |
| How often | Weekly for precision, monthly for recall |
| Owner | Detection engineering, with the quality team |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`precision = correct actions ÷ all actions; recall = violations caught ÷ all violations (estimated by sampling)`

## Why it matters

Tells you where automation is safe to expand and where it isn't.

## Watch out

A blended figure hides failures in low-volume, high-severity areas.

**Read it with:** [Proactive detection rate](proactive-detection-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Precision: every week, sample each classifier's or rule's actions at random and have experts label them blind.
2. Recall: sample content the system did not flag, label it, and scale up the misses you find to estimate what you missed overall.
3. Report both per policy area and per model version, so a model change shows up immediately.
4. Set a precision floor per area (for example 95% for automatic removal) before automation can act without a human.

**Sample size.** At an expected rate of 90% and a margin of ±3 points (95% confidence), label about 385 random items. Halving the margin needs four times as many.

### On your platform

- **On a social platform:** Measure per language as well as per policy. Classifiers usually do worst in languages with little training data.
- **On a marketplace:** Measure precision on blocked listings per category, and learn from the evidence in upheld seller appeals.
- **In a game:** Voice classifiers need their own precision and recall. They behave differently from text.
- **In a generative AI product:** Measure recall with your evaluation suite, and precision by sampling blocked prompts and outputs in production.
- **In a fintech or payments product:** Precision is the share of held or blocked payments that really were fraud. Every false positive is a customer who couldn't pay.

### What you need to log

- **QA re-reviews** (`qa_reviews`): one row is one decision re-reviewed blind by an expert.
- **Automated detections** (`detections`): one row is one flag from a classifier, hash match or rule.
- **Random exposure sample** (`exposure_sample`): one row is one randomly sampled view (or live listing), labeled by a trained reviewer.

### Worked example

372 of 400 sampled automated removals were correct: 93% precision. Labeling 20,000 of 10 million unflagged posts finds 9 misses, about 4,500 overall. With 18,000 caught, recall is about 80%.

### No data team yet?

For precision, have a senior reviewer re-check 50 automated actions a week. Leave recall until you can sample.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
-- Precision, from blind QA of automated actions
SELECT a.policy, a.model_version,
  AVG(CASE WHEN q.expert_label = 'violating' THEN 1.0 ELSE 0 END) AS precision_rate
FROM qa_reviews q
JOIN enforcement_actions a ON a.action_id = q.action_id
WHERE a.source = 'automated'
GROUP BY a.policy, a.model_version;
-- Recall ≈ caught ÷ (caught + misses scaled up from the unflagged sample)
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
