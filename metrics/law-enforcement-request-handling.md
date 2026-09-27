# Law-enforcement request handling

> **Do we handle police and government requests fast and carefully?**

Time to respond to law-enforcement and government requests for user data or removal, and the share disclosed, narrowed or rejected after review.

| | |
|---|---|
| Area | Compliance |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Monthly; emergencies tracked case by case |
| Owner | Legal (law-enforcement response) |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`responded_at − received_at per request, with the share disclosed, narrowed or rejected`

## Why it matters

Fast help in emergencies can save lives, and careful review of every request protects users from overreach. Both go in your transparency report.

## Watch out

Speed without legal review creates risk. Measure emergency requests separately from routine ones.

**Read it with:** [Time to report child sexual exploitation](time-to-report-child-sexual-exploitation.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Log every request in one intake: type (emergency, subpoena, court order, removal demand), country, agency and time received.
2. Send emergencies (imminent risk of death or serious injury) to an on-call responder with a target measured in hours.
3. Have legal review every routine request for validity and scope, and record the outcome: disclosed, narrowed or rejected.
4. Report volumes, outcomes and response times by country. This is what transparency reports publish.

### On your platform

- **In a fintech or payments product:** Freeze orders and legal holds need their own track, separate from data disclosures.
- **In a generative AI product:** Requests may cover prompts and generated content. Decide what you retain, and document it.

### What you need to log

- **Law-enforcement requests** (`le_requests`): one row is one request from police, a court or a government agency.

### Worked example

Emergency requests are answered in 2 hours at p90 and routine ones in 12 days. 18% were narrowed or rejected after legal review, which goes in your transparency report.

### No data team yet?

A shared inbox for legal requests, and a sheet logging type, country, received, answered and outcome.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT type, country,
  COUNT(*) AS requests,
  PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY responded_at - received_at) AS p90_response,
  AVG(CASE WHEN outcome IN ('narrowed', 'rejected') THEN 1.0 ELSE 0 END) AS pushed_back
FROM le_requests
WHERE received_at >= CURRENT_DATE - INTERVAL '90 days'
GROUP BY type, country;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
