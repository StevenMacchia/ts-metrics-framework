# The metrics

36 metrics in adoption order. Start with the north stars.

## North star metrics

| Metric | The question it answers | Area | Good direction |
|---|---|---|---|
| [Violating-content prevalence](violating-content-prevalence.md) | Out of everything people see, how much breaks our rules? | Harm outcomes | Lower is better |
| [Fraud loss rate](fraud-loss-rate.md) | How much money do we lose to fraud for every dollar that moves through us? | Harm outcomes | Lower is better |
| [Toxicity per 1,000 match-hours](toxicity-per-1-000-match-hours.md) | How often do players run into abuse while they play? | Harm outcomes | Lower is better |
| [Violating-generation rate](violating-generation-rate.md) | How often does our AI produce something it shouldn't? | Harm outcomes | Lower is better |
| [Romance-scam reports per 10k matches](romance-scam-reports-per-10k-matches.md) | How often are our users targeted by romance scammers? | Harm outcomes | Lower is better |
| [Account takeover rate](account-takeover-rate.md) | How often do attackers take over our users' accounts? | Harm outcomes | Lower is better |
| [Unsafe-contact rate for minors](unsafe-contact-rate-for-minors.md) | How often do adults try to contact children inappropriately on our product? | Harm outcomes | Lower is better |
| [Safety incidents per 10k trips](safety-incidents-per-10k-trips.md) | How often is someone hurt or put in danger during a trip or booking? | Harm outcomes | Lower is better |
| [Users who feel safe](users-who-feel-safe.md) | Do our users actually feel safe here? | Harm outcomes | Higher is better |

## Health metrics

| Metric | The question it answers | Area | Good direction |
|---|---|---|---|
| [Harmful reach before action](harmful-reach-before-action.md) | How many people see harmful content before we take it down? | Harm outcomes | Lower is better |
| [Prohibited-listing prevalence](prohibited-listing-prevalence.md) | How much of what's for sale shouldn't be? | Harm outcomes | Lower is better |
| [Over-refusal rate](over-refusal-rate.md) | How often does our AI refuse perfectly reasonable requests? | Decision quality | Lower is better |
| [Jailbreak success rate](jailbreak-success-rate.md) | How often can attackers trick our AI into breaking its rules? | Detection | Lower is better |
| [Verified-profile share](verified-profile-share.md) | How many of our active users have proven who they are? | Detection | Higher is better |
| [Proactive detection rate](proactive-detection-rate.md) | How much harm do we catch before anyone has to report it? | Detection | Higher is better |
| [Precision and recall by policy area](precision-and-recall-by-policy-area.md) | When our systems act, are they right, and how much do they miss? | Detection | Higher is better |
| [Appeal overturn rate](appeal-overturn-rate.md) | When someone appeals, how often were we wrong? | Decision quality | Lower is better |
| [QA agreement rate](qa-agreement-rate.md) | Do our reviewers make the same call an expert would? | Decision quality | Higher is better |
| [Time to action by severity (p90)](time-to-action-by-severity-p90.md) | How fast do we act on the most serious problems? | Operations | Lower is better |
| [SLA attainment](sla-attainment.md) | Do we hit the response times we promised? | Operations | Higher is better |
| [Graphic-exposure hours per reviewer](graphic-exposure-hours-per-reviewer.md) | How much disturbing content is each reviewer seeing? | People | Lower is better |
| [Statement-of-reasons coverage](statement-of-reasons-coverage.md) | When we restrict someone, do we properly tell them why? | Compliance | Higher is better |
| [Illegal-content notice handling time](illegal-content-notice-handling-time.md) | How fast do we respond when someone formally reports illegal content? | Compliance | Lower is better |
| [Age-assurance coverage](age-assurance-coverage.md) | Do we actually know which of our users are children? | Detection | Higher is better |
| [Time to report child sexual exploitation](time-to-report-child-sexual-exploitation.md) | How quickly do we report child exploitation to the authorities? | Compliance | Lower is better |
| [Good users wrongly actioned](good-users-wrongly-actioned.md) | How many innocent users do we hurt by mistake? | Decision quality | Lower is better |

## Diagnostic metrics

| Metric | The question it answers | Area | Good direction |
|---|---|---|---|
| [Churn after toxic exposure](churn-after-toxic-exposure.md) | Do people quit after they've been abused? | Harm outcomes | Lower is better |
| [User-report rate](user-report-rate.md) | How often do users flag problems to us? | Harm outcomes | Keep it in a healthy range |
| [Repeat-offender rate](repeat-offender-rate.md) | After we act, do people stop breaking the rules or keep going? | Harm outcomes | Lower is better |
| [Time to detect emerging trends](time-to-detect-emerging-trends.md) | How long does a new kind of abuse run before we notice it? | Detection | Lower is better |
| [Consistency across languages and markets](consistency-across-languages-and-markets.md) | Are we as accurate in every language as we are in our best one? | Decision quality | Higher is better |
| [Backlog age](backlog-age.md) | Is work piling up faster than we can handle it? | Operations | Lower is better |
| [Cost per decision](cost-per-decision.md) | What does each moderation decision cost us? | Operations | Keep it in a healthy range |
| [Attrition and wellness-support usage](attrition-and-wellness-support-usage.md) | Are we burning out the people who keep users safe? | People | Lower is better |
| [Systemic-risk assessment currency](systemic-risk-assessment-currency.md) | Are our legally required risk assessments up to date? | Compliance | Lower is better |
| [Law-enforcement request handling](law-enforcement-request-handling.md) | Do we handle police and government requests fast and carefully? | Compliance | Lower is better |

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
