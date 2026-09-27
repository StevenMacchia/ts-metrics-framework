# Time to detect emerging trends

> **How long does a new kind of abuse run before we notice it?**

Time from a new harmful trend's first appearance to its formal triage.

| | |
|---|---|
| Area | Detection |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Quarterly, filled in at every post-incident review |
| Owner | Intelligence or threat analysis |
| Platforms | All platforms |
| Program stage | Mature and later |

## The formula

`formal triage time − time the first related item appeared`

## Why it matters

Crises are usually won or lost in this window.

## Watch out

Hard to measure without post-incident reviews. Log it in every one.

**Read it with:** [User-report rate](user-report-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. After every incident or new trend, search back for the earliest related item: the first post, listing or account using the pattern.
2. Record three timestamps in the incident log: first appearance, first internal signal (a report spike, alert or analyst note) and formal triage.
3. Report the median across incidents each quarter, with the signal-to-triage gap shown on its own.
4. The signal-to-triage gap is usually the fastest to fix. It is about alerting and on-call, not detection.

### On your platform

- **On a social platform:** Trends follow the news. Keep an events calendar and watch report spikes around elections, conflicts and viral challenges.
- **On a marketplace:** Watch for new listing keywords and bursts of new sellers in one category. That is how counterfeit waves start.
- **In a game:** New exploits and scams follow game updates and item drops. Check signals closely in the days after each release.
- **In a generative AI product:** New jailbreaks spread on public forums and social media. Someone should check them daily.
- **In a fintech or payments product:** New scam scripts show up first in customer-support contacts. Tag and review them weekly.

### What you need to log

- **Incident log** (`incidents`): one row is one incident or emerging trend, with its timeline.

### Worked example

A new scam script first appeared 11 days before triage, but reports spiked on day 3. The fix was an alert on report spikes, not a new classifier.

### No data team yet?

Add "When did this actually start?" to your post-incident template.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT DATE_TRUNC('quarter', triaged_at) AS quarter,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY triaged_at - first_item_at)
    AS median_time_to_detect,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY triaged_at - first_signal_at)
    AS median_signal_to_triage
FROM incidents
GROUP BY 1 ORDER BY 1;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
