# Graphic-exposure hours per reviewer

> **How much disturbing content is each reviewer seeing?**

Hours per reviewer per week spent on graphic or egregious content queues.

| | |
|---|---|
| Area | People |
| Tier | Health |
| Good direction | Lower is better |
| How often | Weekly |
| Owner | T&S leadership, with wellness partners |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`hours in graphic or egregious queues per reviewer per week`

## Why it matters

The most direct wellness risk you control.

## Watch out

Averages hide individuals. Cap and monitor each person.

**Read it with:** [Attrition and wellness-support usage](attrition-and-wellness-support-usage.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Tag each queue with an exposure level. Graphic violence, child safety and self-harm are high.
2. Log time in queue per reviewer from your case tool, not from self-reporting.
3. Agree a weekly cap per person with your wellness partner, and alert team leads when anyone approaches it.
4. Report the distribution and the number of people over the cap, never only the team average.

### On your platform

- **In a game:** Voice review can be as hard as graphic video. Count it in exposure hours.
- **In a generative AI product:** Red-teamers and evaluation graders are exposed too. Include them in caps and wellness support.
- **On a product for kids or education:** Child-safety queues carry the highest exposure. Rotate reviewers and cap hours strictly.

### What you need to log

- **Workforce and cost** (`reviewer_weeks`): one row is one reviewer-week, plus monthly cost and headcount rollups.

### Worked example

The team averages 9 hours a week, but 6 people passed the 15-hour cap because they were the only ones trained for the child-safety queue. The fix is cross-training.

### No data team yet?

Rotate people off graphic queues on a fixed schedule, and keep a simple rota log.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT week,
  PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY graphic_hours) AS p90_hours,
  COUNT(*) FILTER (WHERE graphic_hours > 15) AS reviewers_over_cap  -- 15 = your agreed cap
FROM reviewer_weeks
GROUP BY week
ORDER BY week DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
