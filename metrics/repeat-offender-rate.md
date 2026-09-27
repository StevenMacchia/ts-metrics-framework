# Repeat-offender rate

> **After we act, do people stop breaking the rules or keep going?**

Share of enforcement actions against accounts with a prior violation within 90 days.

| | |
|---|---|
| Area | Harm outcomes |
| Tier | Diagnostic |
| Good direction | Lower is better |
| How often | Monthly |
| Owner | Policy, with T&S operations |
| Platforms | All platforms |
| Program stage | Scaling and later |

## The formula

`actions on accounts with a prior violation in the last 90 days ÷ all actions`

## Why it matters

Tests whether your penalty ladder actually changes behavior.

## Watch out

Ban evasion hides repeat offenders behind new accounts.

**Read it with:** [Appeal overturn rate](appeal-overturn-rate.md). Any metric can be gamed; this one shows when that's happening.

## How to measure it

1. For each action, look back 90 days on the same account for an earlier confirmed violation.
2. Split by the earlier penalty (warning, restriction, suspension) to see which penalties actually deter.
3. Link accounts by device, payment and contact signals where your privacy policy allows, so ban evasion is counted.
4. Report per policy area. Spam and harassment behave very differently.

### On your platform

- **On a social platform:** Link accounts by device and phone number to catch people who return after a ban.
- **On a marketplace:** Sellers come back under new store names. Link by payout bank account, which is hard to replace.
- **In a game:** New accounts are free. Link by hardware ID and payment method.
- **On a dating app:** Link by phone number, device and photo matching. Banned scammers often reuse the same photos.
- **In a generative AI product:** Track accounts and API keys that repeatedly hit policy blocks. They are often probing for a jailbreak.
- **In a fintech or payments product:** Link by identity document, device and bank details. Returning fraudsters often use mule accounts in real people's names.
- **On a gig, delivery or rental platform:** Deactivated workers come back through someone else's account. Real-time selfie checks catch it.

### What you need to log

- **Decisions and enforcement** (`enforcement_actions`): one row is one decision on one object: a post, listing, message or account.

### Worked example

31% of harassment actions hit accounts warned in the last 90 days, but only 8% follow a 7-day suspension. Warnings are not deterring anyone.

### No data team yet?

Add a "prior strike in the last 90 days?" checkbox to your case form.

### Starter SQL

Postgres-style, on an example schema. Rename tables and fields to match your warehouse.

```sql
SELECT a.policy,
  AVG(CASE WHEN EXISTS (
        SELECT 1 FROM enforcement_actions p
        WHERE p.account_id = a.account_id
          AND p.decided_at <  a.decided_at
          AND p.decided_at >= a.decided_at - INTERVAL '90 days')
      THEN 1.0 ELSE 0 END) AS repeat_share
FROM enforcement_actions a
WHERE a.decided_at >= CURRENT_DATE - INTERVAL '28 days'
GROUP BY a.policy;
```

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
