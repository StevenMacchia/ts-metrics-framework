# Data to log

Every metric comes from a handful of event logs. Most teams can't measure well because a timestamp or a source field was never logged. Instrument these first.

## Decisions and enforcement

Table `enforcement_actions`. One row is one decision on one object: a post, listing, message or account.

**Fields:** `action_id`, `object_id`, `account_id`, `policy`, `severity`, `action`, `source`, `first_seen_at`, `decided_at`, `reviewer_id`, `vendor`, `queue`, `language`, `views_at_action`

first_seen_at (the first report or detection) and source (automated, proactive or user report) are the two fields teams most often forget.

**Used by:** [Toxicity per 1,000 match-hours](metrics/toxicity-per-1-000-match-hours.md), [Romance-scam reports per 10k matches](metrics/romance-scam-reports-per-10k-matches.md), [Unsafe-contact rate for minors](metrics/unsafe-contact-rate-for-minors.md), [Safety incidents per 10k trips](metrics/safety-incidents-per-10k-trips.md), [Harmful reach before action](metrics/harmful-reach-before-action.md), [Prohibited-listing prevalence](metrics/prohibited-listing-prevalence.md), [Verified-profile share](metrics/verified-profile-share.md), [Proactive detection rate](metrics/proactive-detection-rate.md), [Appeal overturn rate](metrics/appeal-overturn-rate.md), [QA agreement rate](metrics/qa-agreement-rate.md), [Time to action by severity (p90)](metrics/time-to-action-by-severity-p90.md), [SLA attainment](metrics/sla-attainment.md), [Statement-of-reasons coverage](metrics/statement-of-reasons-coverage.md), [Age-assurance coverage](metrics/age-assurance-coverage.md), [Time to report child sexual exploitation](metrics/time-to-report-child-sexual-exploitation.md), [Good users wrongly actioned](metrics/good-users-wrongly-actioned.md), [Churn after toxic exposure](metrics/churn-after-toxic-exposure.md), [Repeat-offender rate](metrics/repeat-offender-rate.md), [Consistency across languages and markets](metrics/consistency-across-languages-and-markets.md), [Backlog age](metrics/backlog-age.md), [Cost per decision](metrics/cost-per-decision.md)

## Usage denominators

Table `daily_usage`. One row is daily totals from product analytics: active users, views, GMV, match-hours, matches.

**Fields:** `day`, `region`, `dau`, `views`, `gmv`, `match_hours`, `matches`

Take denominators from the same source the company reports to the board, so your rates reconcile with theirs.

**Used by:** [Violating-content prevalence](metrics/violating-content-prevalence.md), [Fraud loss rate](metrics/fraud-loss-rate.md), [Toxicity per 1,000 match-hours](metrics/toxicity-per-1-000-match-hours.md), [Romance-scam reports per 10k matches](metrics/romance-scam-reports-per-10k-matches.md), [Account takeover rate](metrics/account-takeover-rate.md), [Unsafe-contact rate for minors](metrics/unsafe-contact-rate-for-minors.md), [Safety incidents per 10k trips](metrics/safety-incidents-per-10k-trips.md), [Users who feel safe](metrics/users-who-feel-safe.md), [Harmful reach before action](metrics/harmful-reach-before-action.md), [Verified-profile share](metrics/verified-profile-share.md), [Age-assurance coverage](metrics/age-assurance-coverage.md), [Good users wrongly actioned](metrics/good-users-wrongly-actioned.md), [Churn after toxic exposure](metrics/churn-after-toxic-exposure.md), [User-report rate](metrics/user-report-rate.md)

## User reports

Table `user_reports`. One row is one report filed by one user.

**Fields:** `report_id`, `object_id`, `reporter_id`, `reason`, `channel`, `created_at`

Log the reason picked and the reporting surface, so you can tell a UX change from a change in harm.

**Used by:** [Toxicity per 1,000 match-hours](metrics/toxicity-per-1-000-match-hours.md), [Romance-scam reports per 10k matches](metrics/romance-scam-reports-per-10k-matches.md), [Account takeover rate](metrics/account-takeover-rate.md), [Unsafe-contact rate for minors](metrics/unsafe-contact-rate-for-minors.md), [Safety incidents per 10k trips](metrics/safety-incidents-per-10k-trips.md), [Proactive detection rate](metrics/proactive-detection-rate.md), [User-report rate](metrics/user-report-rate.md)

## Random exposure sample

Table `exposure_sample`. One row is one randomly sampled view (or live listing), labeled by a trained reviewer.

**Fields:** `view_id`, `content_id`, `surface`, `market`, `sampled_at`, `label`, `policy`, `labeler_id`

Sample views, not items. A post seen a million times should be a million times more likely to be picked.

**Used by:** [Violating-content prevalence](metrics/violating-content-prevalence.md), [Violating-generation rate](metrics/violating-generation-rate.md), [Prohibited-listing prevalence](metrics/prohibited-listing-prevalence.md), [Precision and recall by policy area](metrics/precision-and-recall-by-policy-area.md)

## Automated detections

Table `detections`. One row is one flag from a classifier, hash match or rule.

**Fields:** `detection_id`, `object_id`, `model_id`, `model_version`, `score`, `threshold`, `created_at`

Keep the model version and threshold on every row, or you can never compare before and after a model change.

**Used by:** [Unsafe-contact rate for minors](metrics/unsafe-contact-rate-for-minors.md), [Proactive detection rate](metrics/proactive-detection-rate.md), [Precision and recall by policy area](metrics/precision-and-recall-by-policy-area.md), [Time to report child sexual exploitation](metrics/time-to-report-child-sexual-exploitation.md)

## QA re-reviews

Table `qa_reviews`. One row is one decision re-reviewed blind by an expert.

**Fields:** `qa_id`, `action_id`, `original_label`, `expert_label`, `expert_id`, `sampled_at`

Experts must not see the original decision or reviewer, or agreement will be inflated.

**Used by:** [Precision and recall by policy area](metrics/precision-and-recall-by-policy-area.md), [QA agreement rate](metrics/qa-agreement-rate.md), [Good users wrongly actioned](metrics/good-users-wrongly-actioned.md), [Consistency across languages and markets](metrics/consistency-across-languages-and-markets.md)

## Legal notices and risk register

Table `legal_notices`. One row is one legal notice, or one required risk assessment with its mitigations.

**Fields:** `notice_id`, `source_type`, `received_at`, `decided_at`, `sor_sent`, `assessment_id`, `last_reviewed`

Agree who timestamps receipt. Regulators start the clock when a notice arrives, not when someone opens it.

**Used by:** [Statement-of-reasons coverage](metrics/statement-of-reasons-coverage.md), [Illegal-content notice handling time](metrics/illegal-content-notice-handling-time.md), [Time to report child sexual exploitation](metrics/time-to-report-child-sexual-exploitation.md), [Systemic-risk assessment currency](metrics/systemic-risk-assessment-currency.md)

## Model evaluations

Table `eval_runs`. One row is one prompt run against one model version, with a graded output.

**Fields:** `run_id`, `prompt_id`, `suite`, `harm_area`, `technique`, `model_version`, `output_label`, `grader`

Version the prompt suite as carefully as the model. A silently changed suite makes every trend meaningless.

**Used by:** [Violating-generation rate](metrics/violating-generation-rate.md), [Over-refusal rate](metrics/over-refusal-rate.md), [Jailbreak success rate](metrics/jailbreak-success-rate.md)

## Appeals

Table `appeals`. One row is one appeal against one decision.

**Fields:** `appeal_id`, `action_id`, `filed_at`, `resolved_at`, `outcome`, `resolver_id`

Always join back to the original action, so overturns can be split by policy, source and vendor.

**Used by:** [Appeal overturn rate](metrics/appeal-overturn-rate.md), [Good users wrongly actioned](metrics/good-users-wrongly-actioned.md), [Consistency across languages and markets](metrics/consistency-across-languages-and-markets.md)

## Workforce and cost

Table `reviewer_weeks`. One row is one reviewer-week, plus monthly cost and headcount rollups.

**Fields:** `reviewer_id`, `week`, `queue`, `hours`, `graphic_hours`, `vendor`, `monthly_cost`, `leavers`

Keep wellness data anonymous and aggregated. Anything at the individual level needs HR and privacy sign-off.

**Used by:** [Graphic-exposure hours per reviewer](metrics/graphic-exposure-hours-per-reviewer.md), [Cost per decision](metrics/cost-per-decision.md), [Attrition and wellness-support usage](metrics/attrition-and-wellness-support-usage.md)

## Losses and payments

Table `fraud_losses`. One row is one chargeback, scam refund or buyer-protection payout.

**Fields:** `loss_id`, `order_id`, `amount`, `reason_code`, `paid_at`

Get this from Finance or Payments, not from T&S tools, and agree the fraud reason codes with them up front.

**Used by:** [Fraud loss rate](metrics/fraud-loss-rate.md)

## Login and security events

Table `auth_events`. One row is one login, password reset, device change or 2-step verification event.

**Fields:** `event_id`, `account_id`, `event_type`, `device_id`, `ip_country`, `risk_score`, `outcome`, `created_at`

Keep device and location on every event. Takeovers show up as a new device followed by a credential change within minutes.

**Used by:** [Account takeover rate](metrics/account-takeover-rate.md)

## Safety surveys

Table `survey_responses`. One row is one answer to an in-product safety survey.

**Fields:** `response_id`, `user_hash`, `asked_at`, `question_id`, `answer`, `segment`, `weight`

Ask the same question the same way every time, and record who was asked, not only who answered.

**Used by:** [Users who feel safe](metrics/users-who-feel-safe.md)

## Incident log

Table `incidents`. One row is one incident or emerging trend, with its timeline.

**Fields:** `incident_id`, `severity`, `first_item_at`, `first_signal_at`, `triaged_at`, `mitigated_at`

Fill it in at every post-incident review while memories are fresh. It is the only source for detection speed.

**Used by:** [Time to detect emerging trends](metrics/time-to-detect-emerging-trends.md)

## Law-enforcement requests

Table `le_requests`. One row is one request from police, a court or a government agency.

**Fields:** `request_id`, `type`, `country`, `agency`, `received_at`, `responded_at`, `outcome`, `emergency`

Log every request, including ones you reject. Rejections are what show you protect users.

**Used by:** [Law-enforcement request handling](metrics/law-enforcement-request-handling.md)

## Where the data usually lives

| Area | Source | Owner | The hard part |
|---|---|---|---|
| Harm outcomes | A labeled random sample of what users see, plus user reports, finance data and retention data. | Data science runs the pipeline. Policy owns the labeling guidelines. Finance owns loss. | Prevalence needs a sampling pipeline and trained labelers. Nothing else tells you what users actually experience. |
| Detection | Detection and enforcement logs, joined to expert QA labels. | Detection or ML engineering, with T&S operations. | Recall needs an estimate of what you missed, and that only comes from labeling content nobody flagged. |
| Decision quality | Appeals and blind expert re-reviews. | A quality team that is independent of the operations team it measures. | Independence. If the people being measured choose the QA sample, the number will always look great. |
| Operations | Your case-management tool: queue timestamps and decision logs. | T&S operations and workforce management. | Consistent clocks. Decide once whether time starts at creation, report or detection, and never change it quietly. |
| People | Workforce management, HR and vendor reports. | T&S leadership with HR and wellness partners. | Privacy. Measure enough to protect people without surveilling them. |
| Compliance | Legal notice intake, statements of reasons and the risk register. | Legal or compliance, with T&S operations. | Evidence. Auditors ask you to prove it, so every step needs a timestamp and a named owner. |

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
