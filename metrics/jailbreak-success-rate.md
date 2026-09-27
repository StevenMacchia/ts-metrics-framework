# Jailbreak success rate

> **How often can attackers trick our AI into breaking its rules?**

Share of known adversarial techniques that get policy-violating output, by technique family.

| | |
|---|---|
| Area | Detection |
| Tier | Health |
| Good direction | Lower is better |
| How often | Every release, and when a new technique is published |
| Owner | Red team |
| Platforms | Generative AI product |
| Program stage | Scaling and later |

## The formula

`attacks that produce violating output ÷ attacks attempted, per technique family`

## Why it matters

Tracks how robust the model is as attacks evolve.

## Watch out

It only covers attacks you already know about. Pair it with external red-teaming.

**Read it with:** [Over-refusal rate](over-refusal-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Group known attacks into families: role-play, encoding, many-shot, prompt injection through documents or tools, multi-turn escalation.
2. Keep a few dozen variants of each family and run all of them on every release.
3. Add new techniques from bug bounty reports, red-team exercises and public research within a week of discovery.
4. Report per family. A blended rate hides the family that works every time.

### On your platform

- **In a generative AI product:** If your product uses tools or reads documents and web pages, test prompt injection through those inputs. Many new attacks arrive that way.

### What you need to log

- **Model evaluations** (`eval_runs`): one row is one prompt run against one model version, with a graded output.

### Worked example

Role-play attacks succeed 2% of the time, but prompt injection through uploaded documents succeeds 18%. That is where engineering time goes next.

### No data team yet?

Keep a list of every attack that has worked on you, and re-run all of them before each release.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT technique, model_version,
  AVG(CASE WHEN output_label = 'violating' THEN 1.0 ELSE 0 END) AS success_rate,
  COUNT(*) AS attempts
FROM eval_runs
WHERE suite = 'jailbreak'
GROUP BY technique, model_version;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
