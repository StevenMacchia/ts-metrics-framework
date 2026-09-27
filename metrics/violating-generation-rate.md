# Violating-generation rate

> **How often does our AI produce something it shouldn't?**

Share of outputs that violate policy, measured on a fixed red-team suite plus a sample of production traffic.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Every release, plus a weekly production sample |
| Owner | Model safety or evaluations team |
| Platforms | Generative AI product |
| Program stage | Early and later |

## The formula

`violating outputs ÷ outputs graded, shown separately for the eval suite and for production samples`

## Why it matters

The core safety measure for a generative product.

## Watch out

Fixed suites get stale. Refresh them quarterly or the model overfits to them.

**Read it with:** [Over-refusal rate](over-refusal-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Keep a versioned evaluation suite of prompts per harm area (for example self-harm, weapons, sexual content, hate), with the expected behavior for each.
2. Run it on every model or system-prompt change, and grade outputs against a rubric. Model graders are fine if you check them against humans.
3. Separately, sample real production conversations at random, remove personal data, and grade them the same way.
4. Report both. The suite catches regressions between releases; production shows what users actually get.

**Sample size.** At an expected rate of 0.5% and a margin of ±0.2 points (95% confidence), label about 4,778 random items. Halving the margin needs four times as many.

### On your platform

- **In a generative AI product:** Grade the whole conversation, not only the last turn. Many failures come after several turns of setup.

### What you need to log

- **Model evaluations** (`eval_runs`): one row is one prompt run against one model version, with a graded output.
- **Random exposure sample** (`exposure_sample`): one row is one randomly sampled view (or live listing), labeled by a trained reviewer.

### Worked example

Version 4.2 produces violating output on 0.6% of the self-harm suite against 0.2% for 4.1. The release waits until it is fixed.

### No data team yet?

A spreadsheet of 200 risky prompts, run by hand before each release and graded by two people.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT suite, harm_area, model_version,
  AVG(CASE WHEN output_label = 'violating' THEN 1.0 ELSE 0 END) AS violating_rate,
  COUNT(*) AS graded
FROM eval_runs
GROUP BY suite, harm_area, model_version
ORDER BY model_version DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
