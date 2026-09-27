# Over-refusal rate

> **How often does our AI refuse perfectly reasonable requests?**

Share of clearly benign prompts that the model refuses or heavily hedges.

| | |
|---|---|
| Area | Decision quality |
| Tier | Health |
| Good direction | Lower is better |
| How often | Every release |
| Owner | Model safety, with product |
| Platforms | Generative AI product |
| Program stage | Early and later |

## The formula

`refused or heavily hedged answers to benign prompts ÷ benign prompts graded`

## Why it matters

The counterweight to safety. Over-refusal is also a harm to users and to the product.

## Watch out

Tracking only the violation rate pushes teams toward refusing everything.

**Read it with:** [Violating-generation rate](violating-generation-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Build a benign suite that looks risky but is fine: medical questions, safety research, history, dark fiction.
2. Grade each answer as helpful, hedged (answers but buries it) or refused.
3. Run it with the violation suite on every release, and chart the two lines together.
4. Sample production conversations where the model refused, and label how many were actually benign.

**Sample size.** At an expected rate of 5% and a margin of ±1.5 points (95% confidence), label about 812 random items. Halving the margin needs four times as many.

### On your platform

- **In a generative AI product:** Measure image and file filters separately from text. They over-block in different ways.

### What you need to log

- **Model evaluations** (`eval_runs`): one row is one prompt run against one model version, with a graded output.

### Worked example

A new filter halves violating output but raises over-refusal from 3% to 11%. The team tunes it before shipping, because users would leave.

### No data team yet?

Add 100 benign but edgy prompts to the same spreadsheet and grade them in the same session.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT model_version,
  AVG(CASE WHEN output_label IN ('refused', 'hedged') THEN 1.0 ELSE 0 END)
    AS over_refusal_rate
FROM eval_runs
WHERE suite = 'benign_borderline'
GROUP BY model_version;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
