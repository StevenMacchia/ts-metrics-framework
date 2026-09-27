# User-report rate

> **How often do users flag problems to us?**

User reports per 1,000 daily active users, by policy area.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | Diagnostic |
| Good direction | Keep it in a healthy range |
| How often | Daily dashboard, weekly review |
| Owner | T&S operations |
| Platforms | All platforms |
| Program stage | Early and later |

## The formula

`user reports ÷ daily active users × 1,000, per policy area`

## Why it matters

Cheap and fast to compute. A useful early-warning signal.

## Watch out

It rises when reporting gets easier. That can mean better UX, not more harm.

**Read it with:** [Violating-content prevalence](violating-content-prevalence.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Count reports, and separately count unique items reported. One viral item can draw thousands of reports.
2. Divide by daily active users for the same day and region.
3. Annotate the chart with every change to the reporting flow. UX changes move this number on their own.
4. Alert on a sudden spike in one reason. It is often the first sign of a new trend or a brigading campaign.

### On your platform

- **On a social platform:** Split reports on posts from reports on accounts. Brigading shows up as many reports against one account.
- **On a marketplace:** Separate reports on listings from disputes about orders. Disputes belong with customer-support metrics.
- **In a game:** Players often report the other team after losing. Compare report rates from winning and losing sides to measure that bias.
- **On a dating app:** Unmatch-and-report is the main signal. Track reports per conversation, not only per user.
- **In a generative AI product:** Users rarely report harmful outputs. Add a thumbs-down with a "harmful" reason and treat it as a weak signal.
- **In a fintech or payments product:** Reports arrive mostly through support calls and chat. Tag those contacts, or this metric will be close to zero.
- **On a gig, delivery or rental platform:** Most reports arrive after the trip, in ratings and support tickets. Count low ratings with safety reasons as reports.
- **On a product for kids or education:** Children under-report. Count reports from parents and teachers too, and make the report button easy for young users to find and understand.

### What you need to log

- **User reports** (`user_reports`): one row is one report filed by one user.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

Reports per 1,000 DAU jump from 4 to 9 overnight, but unique items barely move. It is a brigading campaign against a few accounts, not a rise in harm.

### No data team yet?

Most ticketing tools can chart tickets per day by reason. Divide by DAU in a spreadsheet.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT r.day, r.reason,
  1000.0 * r.reports / u.dau AS reports_per_1k_dau,
  r.unique_items
FROM (SELECT DATE(created_at) AS day, reason,
        COUNT(*) AS reports, COUNT(DISTINCT object_id) AS unique_items
      FROM user_reports GROUP BY 1, 2) r
JOIN daily_usage u ON u.day = r.day;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
