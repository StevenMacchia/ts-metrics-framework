# Running the program

A metric only changes behavior when a named meeting looks at it on a schedule.

## Weekly operations review

Ops leads and vendor managers. Fix this week's problems.

- [QA agreement rate](metrics/qa-agreement-rate.md)
- [Time to action by severity (p90)](metrics/time-to-action-by-severity-p90.md)
- [SLA attainment](metrics/sla-attainment.md)
- [Graphic-exposure hours per reviewer](metrics/graphic-exposure-hours-per-reviewer.md)
- [Illegal-content notice handling time](metrics/illegal-content-notice-handling-time.md)
- [Time to report child sexual exploitation](metrics/time-to-report-child-sexual-exploitation.md)
- [User-report rate](metrics/user-report-rate.md)
- [Backlog age](metrics/backlog-age.md)

## Monthly health review

T&S leadership, policy, detection and quality. Spot trends and assign owners.

- [Toxicity per 1,000 match-hours](metrics/toxicity-per-1-000-match-hours.md)
- [Romance-scam reports per 10k matches](metrics/romance-scam-reports-per-10k-matches.md)
- [Account takeover rate](metrics/account-takeover-rate.md)
- [Unsafe-contact rate for minors](metrics/unsafe-contact-rate-for-minors.md)
- [Harmful reach before action](metrics/harmful-reach-before-action.md)
- [Prohibited-listing prevalence](metrics/prohibited-listing-prevalence.md)
- [Verified-profile share](metrics/verified-profile-share.md)
- [Proactive detection rate](metrics/proactive-detection-rate.md)
- [Precision and recall by policy area](metrics/precision-and-recall-by-policy-area.md)
- [Appeal overturn rate](metrics/appeal-overturn-rate.md)
- [Statement-of-reasons coverage](metrics/statement-of-reasons-coverage.md)
- [Age-assurance coverage](metrics/age-assurance-coverage.md)
- [Good users wrongly actioned](metrics/good-users-wrongly-actioned.md)
- [Repeat-offender rate](metrics/repeat-offender-rate.md)
- [Cost per decision](metrics/cost-per-decision.md)

## Quarterly executive review

Executives, legal and finance. North stars, risk and investment.

- [Violating-content prevalence](metrics/violating-content-prevalence.md)
- [Fraud loss rate](metrics/fraud-loss-rate.md)
- [Safety incidents per 10k trips](metrics/safety-incidents-per-10k-trips.md)
- [Users who feel safe](metrics/users-who-feel-safe.md)
- [Churn after toxic exposure](metrics/churn-after-toxic-exposure.md)
- [Time to detect emerging trends](metrics/time-to-detect-emerging-trends.md)
- [Consistency across languages and markets](metrics/consistency-across-languages-and-markets.md)
- [Attrition and wellness-support usage](metrics/attrition-and-wellness-support-usage.md)
- [Systemic-risk assessment currency](metrics/systemic-risk-assessment-currency.md)
- [Law-enforcement request handling](metrics/law-enforcement-request-handling.md)

## Every release

Model safety and product. Decide whether to ship.

- [Violating-generation rate](metrics/violating-generation-rate.md)
- [Over-refusal rate](metrics/over-refusal-rate.md)
- [Jailbreak success rate](metrics/jailbreak-success-rate.md)

## How to set targets

1. **Baseline first.** Measure for 6 to 8 weeks before you commit to any number. Early targets are guesses.
2. **Set a band, not a point.** Say "prevalence below 0.10%, off track above 0.15%". The gap between the two is your early warning.
3. **Pair every target with its guardrail.** An SLA target without a QA floor rewards rushing. Each metric names its guardrail.
4. **Set thresholds by severity.** A missed tier 1 target is an incident. A missed tier 4 target is a data point.
5. **Show uncertainty.** Anything from a sample gets a confidence interval. Don't celebrate a change that sits inside it.

## Keep these off the executive slide

- **Total items removed.** It rises with volume and with over-enforcement, and says nothing about what users experience.
- **Number of reports received.** It measures how easy reporting is as much as how much harm exists.
- **Automation rate on its own.** "80% automated" means nothing without precision. It can mean 80% wrong at scale.
- **Moderator headcount.** Input, not outcome. Executives will ask why it keeps growing.
- **Accounts banned.** Easy to inflate with throwaway spam accounts, and it rewards churning through bad actors instead of deterring them.

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
