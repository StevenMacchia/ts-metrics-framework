# Systemic-risk assessment currency

> **Are our legally required risk assessments up to date?**

Months since each required risk assessment was last updated, with the status of each mitigation.

| | |
|---|---|
| Area | Compliance |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Monthly check; full review yearly or on any major product change |
| Owner | Compliance or risk |
| Platforms | All platforms |
| Program stage | Scaling and later · where the EU DSA or UK OSA applies |

## The formula

`days since each required risk assessment was reviewed; share of mitigations overdue`

## Why it matters

Regulators test whether your mitigations are real and tracked.

## Watch out

Assessments written once for compliance go stale within a year.

**Read it with:** [Illegal-content notice handling time](illegal-content-notice-handling-time.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Keep a register of every required assessment (for example DSA systemic-risk assessments, or Online Safety Act illegal-content and children's risk assessments) with an owner and last review date.
2. Link each risk to its mitigations, each with an owner, due date and status.
3. Trigger a re-review on any major product change, not only on the calendar.
4. Report the oldest assessment and the share of mitigations overdue.

### On your platform

- **On a product for kids or education:** Under the UK Online Safety Act, services likely to be used by children must keep a children's risk assessment up to date.

### What you need to log

- **Legal notices and risk register** (`legal_notices`): one row is one legal notice, or one required risk assessment with its mitigations.

### Worked example

The children's risk assessment was last reviewed 14 months ago, before direct messaging launched. It needs a re-review now, not at the next annual cycle.

### No data team yet?

One spreadsheet: risk, owner, mitigation, due date, status, last reviewed.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT r.assessment, r.owner, r.last_reviewed,
  CURRENT_DATE - r.last_reviewed AS days_since_review,
  AVG(CASE WHEN m.status = 'overdue' THEN 1.0 ELSE 0 END) AS share_overdue
FROM risk_register r
LEFT JOIN mitigations m ON m.assessment_id = r.assessment_id
GROUP BY r.assessment, r.owner, r.last_reviewed
ORDER BY days_since_review DESC;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
