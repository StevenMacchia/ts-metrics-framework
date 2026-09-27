# Fraud loss rate

> **How much money do we lose to fraud for every dollar that moves through us?**

Fraud losses (chargebacks, scam reimbursements, buyer-protection payouts) in basis points of GMV or payment volume.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | North star |
| Good direction | Lower is better |
| How often | Monthly, with recent months marked provisional |
| Owner | Payments risk or Finance, with T&S |
| Platforms | Marketplace, Fintech & payments, Gig, delivery & rentals |
| Program stage | Early and later |

## The formula

`fraud losses ÷ GMV × 10,000 (basis points)`

## Why it matters

The number Finance already watches. It puts T&S in the P&L conversation.

## Watch out

It lags by weeks because of chargeback windows. Pair it with a leading signal.

**Read it with:** [Verified-profile share](verified-profile-share.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. Agree with Finance which losses count: fraud chargebacks, scam refunds, buyer-protection payouts and goodwill credits.
2. Attribute each loss to the month of the original order (a cohort view), not the month the chargeback arrived.
3. Divide by the GMV of the same order cohort and express it in basis points.
4. Mark the last three months as provisional. They will rise as late chargebacks arrive.

### On your platform

- **On a marketplace:** Include buyer-protection payouts and seller fraud (non-delivery, counterfeit refunds), not only card chargebacks.
- **In a fintech or payments product:** Divide by total payment volume. Split third-party fraud (stolen cards, takeovers) from first-party fraud (customers who lie), and include scam reimbursements where rules require them, such as the UK's authorized push payment scheme.
- **On a gig, delivery or rental platform:** Divide by gross bookings. Include promotion abuse, fake trips and false refund claims, which are often bigger than card fraud.

### What you need to log

- **Losses and payments** (`fraud_losses`): one row is one chargeback, scam refund or buyer-protection payout.
- **Usage denominators** (`daily_usage`): one row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

### Worked example

$180,000 of fraud losses on $60M of GMV is 30 basis points (0.30%).

### No data team yet?

Ask Finance for the monthly chargeback and refund report, and divide it by GMV yourself.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
WITH loss AS (
  SELECT order_id, SUM(amount) AS amt FROM fraud_losses GROUP BY order_id)
SELECT DATE_TRUNC('month', o.ordered_at) AS order_month,
  10000.0 * SUM(COALESCE(loss.amt, 0)) / SUM(o.gmv) AS fraud_loss_bps
FROM orders o
LEFT JOIN loss ON loss.order_id = o.order_id
GROUP BY 1 ORDER BY 1;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
