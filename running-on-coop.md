# Where the metrics' data lives in Coop

For the engineer or analyst setting up logging at a team that uses [Coop](https://github.com/roostorg/coop), ROOST's free, open-source review console. For each log in [data-to-log.md](data-to-log.md), this says where each field comes from: a **Webhook** Coop sends you, an **API** you call, the analytics **Warehouse**, the **App database**, or **Not in Coop** (log it yourself, keyed by Coop's item id and type id). Start with [The gaps](#the-gaps). Based on Coop's docs and source as of 2 October 2026; not tested on a running Coop.

## Decisions and enforcement

Table `enforcement_actions` in [data-to-log.md](data-to-log.md#decisions-and-enforcement). Coop runs the action webhook for every decision, automated or human. The warehouse table `analytics.ACTION_EXECUTIONS` has one row per action per item. Checked in [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `action_id` | Warehouse | No row id. `analytics.ACTION_EXECUTIONS` is keyed by `correlation_id` (the request that caused the action), `action_id` and `item_id`; `job_id` links a manual decision to its job. The webhook carries no execution id, so assign one when you receive it. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md) |
| `object_id` | Webhook | `item.id` with `item.typeId`: Coop identifies an item by the pair. Warehouse: `ACTION_EXECUTIONS.item_id`, `item_type_id`. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [user/concepts.md](https://github.com/roostorg/coop/blob/main/docs/user/concepts.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `account_id` | Webhook | `creator.id`: the content's author, or the user itself for user items; omitted when Coop can't resolve one. Warehouse: `item_creator_id`. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `policy` | Webhook | `policies[].id` and `.name`; several when more than one rule fired, empty for user-strike threshold actions. Warehouse: `policy_ids`, `policy_names`. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `severity` | Webhook | `policies[].penalty`: NONE, LOW, MEDIUM, HIGH or SEVERE, set on the policy in Coop; also from `GET /api/v1/policies/`. Not on the warehouse row: join on policy id. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [api/policies.md](https://github.com/roostorg/coop/blob/main/docs/api/policies.md) |
| `action` | Webhook | `action.id`; names and types from `GET /api/v1/actions/`. Warehouse: `action_id`, `action_name`. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `source` | Webhook | `actorEmail` present means a person decided. `rules` lists the rules that fired and is empty for manual review and bulk actions; user-strike actions have no actor and empty `rules` and `policies`. Warehouse: `action_source` (`mrt-decision`, `post-items`, `user-strike-action-execution`, `retroaction`, `post-actions` and others). To split manual decisions into user report and proactive, join `job_id` to `manual_review_tool.job_creations.enqueue_source_info.kind` (`REPORT`, `RULE_EXECUTION`, `APPEAL`, ...) in the app database. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ActionExecutionLogger.ts](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ActionExecutionLogger.ts), [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql), [manualReviewToolService.ts](https://github.com/roostorg/coop/blob/main/server/services/manualReviewToolService/manualReviewToolService.ts) |
| `first_seen_at` | Warehouse | Not in the webhook. Reports: `REPORTING_SERVICE.REPORTS.reported_at` (your `reportedAt`). Rule hits: `analytics.RULE_EXECUTIONS.ts` where `passed = 1`. Jobs: `manual_review_tool.job_creations.created_at` in the app database. Take the earliest per item. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `decided_at` | Warehouse | `ACTION_EXECUTIONS.ts`. App database: `manual_review_decisions.created_at`, or `dim_mrt_decisions_materialized.decided_at`. The webhook has no timestamp: stamp it on receipt. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md) |
| `reviewer_id` | Webhook | `actorEmail`, omitted for automated actions. Warehouse: `actor_id` (Coop user id). App database: `manual_review_decisions.reviewer_id`. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `vendor` | Not in Coop | Coop users have roles (for example External Moderator), not a vendor. Keep a reviewer-to-vendor table on your side. | [user/administration.md](https://github.com/roostorg/coop/blob/main/docs/user/administration.md) |
| `queue` | App database | `manual_review_decisions.queue_id` and `job_creations.queue_id`; the Recent Decisions export also filters by queue. Not in the webhook; `ACTION_EXECUTIONS` has only `job_id`. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql), [user/metrics.md](https://github.com/roostorg/coop/blob/main/docs/user/metrics.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `language` | Not in Coop | Coop has no language field. Add one to your item type schema and it travels in `item_data` on `RULE_EXECUTIONS` and `CONTENT_API_REQUESTS` rows, though not on `ACTION_EXECUTIONS`. | [user/concepts.md](https://github.com/roostorg/coop/blob/main/docs/user/concepts.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `views_at_action` | Not in Coop | Coop never sees view counts. Record the count when you receive the webhook. |  |

## Usage denominators

Table `daily_usage` in [data-to-log.md](data-to-log.md#usage-denominators). Not in Coop. `CONTENT_API_REQUESTS` counts items you submitted, not views or active users; take denominators from product analytics. Checked in [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## User reports

Table `user_reports` in [data-to-log.md](data-to-log.md#user-reports). Everything you send to the Report API lands in `REPORTING_SERVICE.REPORTS`. Checked in [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `report_id` | API | `POST /api/v1/report` returns `reportId`. Warehouse: `REPORTING_SERVICE.REPORTS`, keyed by `request_id`. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `object_id` | API | `reportedItem.id` and `.typeId` in your request. Warehouse: `reported_item_id`, `reported_item_type_id`. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `reporter_id` | API | `reporter.id`. Warehouse: `reporter_user_id`. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `reason` | API | `reportedForReason.policyId`, `.reason` (free text) and `.csam`. Warehouse: `policy_id`, `reported_for_reason`. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `channel` | Not in Coop | The Report API has no field for the reporting surface. Log it on your side with the `reportId`. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md) |
| `created_at` | API | `reportedAt` (you set it). Warehouse: `reported_at`, plus `ts` for when Coop stored it. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |

## Random exposure sample

Table `exposure_sample` in [data-to-log.md](data-to-log.md#random-exposure-sample). Coop never sees views, so the sample itself is yours. You can label it in Coop: the Report API is also for triggering manual labeling, so send each sampled item to a labeling queue and read the decision back. Checked in [user/concepts.md](https://github.com/roostorg/coop/blob/main/docs/user/concepts.md).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `view_id` | Not in Coop | Log it yourself. |  |
| `content_id` | API | `reportedItem.id` if you send sampled items to a labeling queue through the Report API. | [api/report.md](https://github.com/roostorg/coop/blob/main/docs/api/report.md), [user/concepts.md](https://github.com/roostorg/coop/blob/main/docs/user/concepts.md) |
| `surface` | Not in Coop | Log it yourself. |  |
| `market` | Not in Coop | Log it yourself. |  |
| `sampled_at` | Not in Coop | Log it yourself. |  |
| `label` | App database | The labeling queue's decision in `manual_review_decisions` (`decision_components`), if you label in Coop. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `policy` | App database | Same row, or `dim_mrt_decisions_materialized.policy_id`. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `labeler_id` | App database | `manual_review_decisions.reviewer_id`. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |

## Automated detections

Table `detections` in [data-to-log.md](data-to-log.md#automated-detections). `analytics.RULE_EXECUTIONS` has one row per rule per submitted item, matched or not (`passed`). Signal scores sit inside the `result` JSON, one entry per condition. Checked in [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `detection_id` | Warehouse | No row id. Key `RULE_EXECUTIONS` on `correlation_id`, `rule_id` and `item_id`. Only matches that trigger an action reach the webhook. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md) |
| `object_id` | Warehouse | `RULE_EXECUTIONS.item_id`, `item_type_id`. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `model_id` | Warehouse | `rule_id` and `rule` (its name). The signal behind each condition is inside `result`: `signal.type`, `signal.id`, `signal.name`. `analytics.ITEM_MODEL_SCORES_LOG.model_id` is in the schema too, but the API docs don't say what fills it; check your deployment before relying on it. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [ruleExecutionLoggingUtils.ts](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ruleExecutionLoggingUtils.ts), [development/data-warehouse.md](https://github.com/roostorg/coop/blob/main/docs/development/data-warehouse.md) |
| `model_version` | Warehouse | `rule_version` (rules are versioned). Signals carry no version in the logged condition. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [ruleExecutionLoggingUtils.ts](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ruleExecutionLoggingUtils.ts) |
| `score` | Warehouse | Inside `result`: each condition's `result.score` (a string) and `result.outcome` (PASSED, FAILED, INAPPLICABLE or ERRORED). | [ruleExecutionLoggingUtils.ts](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ruleExecutionLoggingUtils.ts), [conditionResults.ts](https://github.com/roostorg/coop/blob/main/server/services/moderationConfigService/types/conditionResults.ts) |
| `threshold` | Warehouse | Inside `result`: each condition's `comparator` and `threshold`. | [ruleExecutionLoggingUtils.ts](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ruleExecutionLoggingUtils.ts) |
| `created_at` | Warehouse | `RULE_EXECUTIONS.ts`. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |

## QA re-reviews

Table `qa_reviews` in [data-to-log.md](data-to-log.md#qa-re-reviews). Coop has no blind re-review. Its Recent Decisions export is meant for sampling decisions to QA. One way to collect the expert label in Coop: send sampled items through the Report API to a QA-only queue with reviewer access limited to experts; the decision lands in `manual_review_decisions` with that `queue_id`. Check what the expert can see first: the job view shows the user's other content and the report history, so it may not be blind. Checked in [user/metrics.md](https://github.com/roostorg/coop/blob/main/docs/user/metrics.md), [user/review-console.md](https://github.com/roostorg/coop/blob/main/docs/user/review-console.md), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `qa_id` | Not in Coop | Log it yourself. |  |
| `action_id` | Warehouse | The decision you sampled, from the Recent Decisions export or `ACTION_EXECUTIONS`. | [user/metrics.md](https://github.com/roostorg/coop/blob/main/docs/user/metrics.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `original_label` | Warehouse | The sampled row's `policy_ids` and `action_id`. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `expert_label` | Not in Coop | Your log, or the QA queue's `manual_review_decisions` if you label in Coop (see the note above). | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `expert_id` | Not in Coop | Your log, or `manual_review_decisions.reviewer_id` in the QA queue. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `sampled_at` | Not in Coop | Your log, or `job_creations.created_at` for the QA job. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |

## Legal notices and risk register

Table `legal_notices` in [data-to-log.md](data-to-log.md#legal-notices-and-risk-register). Not in Coop. The Recent Decisions export is built for transparency reports, but Coop does not record whether a statement of reasons went out; log `sor_sent` where your webhook handler sends the notice. Checked in [user/metrics.md](https://github.com/roostorg/coop/blob/main/docs/user/metrics.md).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## Model evaluations

Table `eval_runs` in [data-to-log.md](data-to-log.md#model-evaluations). Not in Coop. Coop's backtests replay a rule over past items (`public.backtests`); they test rules, not a model against a prompt suite. Checked in [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql), [user/administration.md](https://github.com/roostorg/coop/blob/main/docs/user/administration.md).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## Appeals

Table `appeals` in [data-to-log.md](data-to-log.md#appeals). Filing is in the warehouse; the outcome comes back on the appeal decision callback and sits in the app database. Checked in [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `appeal_id` | API | Your `appealId` on `POST /api/v1/report/appeal`, echoed in the appeal decision callback. Warehouse: `REPORTING_SERVICE.APPEALS.appeal_id`. | [api/appeal.md](https://github.com/roostorg/coop/blob/main/docs/api/appeal.md), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `action_id` | API | `actionsTaken` (Coop action ids, not execution ids) with `actionedItem`. Warehouse: `actions_taken`, `actioned_item_id`. Join to the enforcement row on item and action. | [api/appeal.md](https://github.com/roostorg/coop/blob/main/docs/api/appeal.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `filed_at` | API | `appealedAt`. Warehouse: `appealed_at`. | [api/appeal.md](https://github.com/roostorg/coop/blob/main/docs/api/appeal.md), [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `resolved_at` | App database | `manual_review_decisions.created_at` for the appeal job. The appeal decision callback has no timestamp: stamp it on receipt. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md) |
| `outcome` | Webhook | `appealDecision` in the appeal decision callback: `ACCEPT` (overturned) or `REJECT` (upheld). App database: decision types `ACCEPT_APPEAL` and `REJECT_APPEAL`. | [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md), [user/appeals.md](https://github.com/roostorg/coop/blob/main/docs/user/appeals.md), [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `resolver_id` | App database | `manual_review_decisions.reviewer_id` on the appeal job; also in the Recent Decisions export. Not in the callback. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql), [user/metrics.md](https://github.com/roostorg/coop/blob/main/docs/user/metrics.md), [api/actions.md](https://github.com/roostorg/coop/blob/main/docs/api/actions.md) |

## Workforce and cost

Table `reviewer_weeks` in [data-to-log.md](data-to-log.md#workforce-and-cost). Who decided what, in which queue, is in the app database. Hours, cost and wellness are not.

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `reviewer_id` | App database | `manual_review_decisions.reviewer_id` (Coop user id). | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `week` | App database | Derive from `manual_review_decisions.created_at`. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `queue` | App database | `manual_review_decisions.queue_id`. | [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql) |
| `hours` | Not in Coop | Coop has handle time per decision (`assigned_at` to `created_at`, which is how its own handle-time metric works), not hours worked. Hours come from your workforce tool. | [Postgres: assigned_at](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2026.08.11T02.10.54.add_mrt_job_claims_and_assigned_at.sql), [DecisionAnalytics.ts](https://github.com/roostorg/coop/blob/main/server/services/manualReviewToolService/modules/DecisionAnalytics.ts) |
| `graphic_hours` | Not in Coop | Log it yourself. |  |
| `vendor` | Not in Coop | Log it yourself. |  |
| `monthly_cost` | Not in Coop | Log it yourself. |  |
| `leavers` | Not in Coop | Log it yourself. |  |

## Losses and payments

Table `fraud_losses` in [data-to-log.md](data-to-log.md#losses-and-payments). Not in Coop. Losses come from Finance or Payments, not from the console.

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## Login and security events

Table `auth_events` in [data-to-log.md](data-to-log.md#login-and-security-events). Not in Coop. `CONTENT_API_REQUESTS.item_ip_address` holds an IP when your item schema marks a field with the IP role, but that is per submitted item, not a login event. Checked in [ClickHouse: item_ip_address](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2026.06.09T22.57.32.add_item_ip_address_to_content_api_requests.sql).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## Safety surveys

Table `survey_responses` in [data-to-log.md](data-to-log.md#safety-surveys). Not in Coop.

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## Incident log

Table `incidents` in [data-to-log.md](data-to-log.md#incident-log). Coop has no incident record. Once you know which items an incident involved, two timestamps can be recovered from the warehouse.

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| `incident_id` | Not in Coop | Log it yourself. |  |
| `severity` | Not in Coop | Log it yourself. |  |
| `first_item_at` | Warehouse | Earliest `CONTENT_API_REQUESTS.ts` for the items you tie to the incident: when you submitted them, not when they were posted. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `first_signal_at` | Warehouse | Earliest `REPORTING_SERVICE.REPORTS.reported_at` or `RULE_EXECUTIONS.ts` with `passed = 1` for those items. | [ClickHouse schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql) |
| `triaged_at` | Not in Coop | Log it yourself. |  |
| `mitigated_at` | Not in Coop | Log it yourself. |  |

## Law-enforcement requests

Table `le_requests` in [data-to-log.md](data-to-log.md#law-enforcement-requests). Not in Coop. Coop's NCMEC integration stores the CyberTips you file (`ncmec_reporting.ncmec_reports`: `report_id`, `user_id`, `created_at`, `reviewer_id`, `incident_type`, `is_test`). Those are outbound reports, not inbound requests, so keep them out of this table. Checked in [user/child-safety.md](https://github.com/roostorg/coop/blob/main/docs/user/child-safety.md), [Postgres schema](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql).

| Field | Where | In Coop | Checked in |
|---|---|---|---|
| all fields | Not in Coop | Log them yourself. | |

## The gaps

- **No exposure or usage data.** Coop sees the items you submit, not views or active users. The exposure sample, `views_at_action` and every denominator stay on your side.
- **No QA layer.** There is no blind re-review and no expert label. The Recent Decisions export gives you the sample; the rest is yours.
- **No incident log, legal notices, law-enforcement requests, eval runs, workforce costs, fraud losses, login events or surveys.** None of these were ever the console's job.
- **No timestamps on the action webhook or the appeal callback.** Stamp both on receipt and treat the warehouse `ts` as the record.
- **No execution id.** The webhook has none and `ACTION_EXECUTIONS` has no row id. Key on `correlation_id`, `action_id` and `item_id`, or assign your own id in the webhook handler.
- **Severity lives on the policy** (`penalty`), not on the decision. `vendor`, `language` and `channel` are not fields anywhere in Coop.
- **Analytics rows exist only if the warehouse is on.** `ANALYTICS_ADAPTER=noop` turns the writes off while keeping the warehouse, so check the setting before trusting a count of zero.

## Sources

Files in [roostorg/coop](https://github.com/roostorg/coop), `main` branch, read on 2 October 2026. Coop keeps records in its app database (Postgres, where jobs and decisions live) and an analytics warehouse (ClickHouse, or Postgres with `WAREHOUSE_ADAPTER=postgresql`, which uses the same table names in lower case). Column names are quoted as the ClickHouse DDL spells them.

- api/actions.md: [`docs/api/actions.md`](https://github.com/roostorg/coop/blob/main/docs/api/actions.md)
- api/items.md: [`docs/api/items.md`](https://github.com/roostorg/coop/blob/main/docs/api/items.md)
- api/report.md: [`docs/api/report.md`](https://github.com/roostorg/coop/blob/main/docs/api/report.md)
- api/appeal.md: [`docs/api/appeal.md`](https://github.com/roostorg/coop/blob/main/docs/api/appeal.md)
- api/policies.md: [`docs/api/policies.md`](https://github.com/roostorg/coop/blob/main/docs/api/policies.md)
- user/concepts.md: [`docs/user/concepts.md`](https://github.com/roostorg/coop/blob/main/docs/user/concepts.md)
- user/review-console.md: [`docs/user/review-console.md`](https://github.com/roostorg/coop/blob/main/docs/user/review-console.md)
- user/metrics.md: [`docs/user/metrics.md`](https://github.com/roostorg/coop/blob/main/docs/user/metrics.md)
- user/appeals.md: [`docs/user/appeals.md`](https://github.com/roostorg/coop/blob/main/docs/user/appeals.md)
- user/administration.md: [`docs/user/administration.md`](https://github.com/roostorg/coop/blob/main/docs/user/administration.md)
- user/child-safety.md: [`docs/user/child-safety.md`](https://github.com/roostorg/coop/blob/main/docs/user/child-safety.md)
- development/data-warehouse.md: [`docs/development/data-warehouse.md`](https://github.com/roostorg/coop/blob/main/docs/development/data-warehouse.md)
- ClickHouse schema: [`db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql`](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2025.11.22T00.00.00.initial-schema.sql)
- ClickHouse: item_ip_address: [`db/src/scripts/clickhouse/2026.06.09T22.57.32.add_item_ip_address_to_content_api_requests.sql`](https://github.com/roostorg/coop/blob/main/db/src/scripts/clickhouse/2026.06.09T22.57.32.add_item_ip_address_to_content_api_requests.sql)
- Postgres schema: [`db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql`](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2025.12.01T00.00.00.initial-schema.sql)
- Postgres: assigned_at: [`db/src/scripts/api-server-pg/2026.08.11T02.10.54.add_mrt_job_claims_and_assigned_at.sql`](https://github.com/roostorg/coop/blob/main/db/src/scripts/api-server-pg/2026.08.11T02.10.54.add_mrt_job_claims_and_assigned_at.sql)
- ActionExecutionLogger.ts: [`server/services/analyticsLoggers/ActionExecutionLogger.ts`](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ActionExecutionLogger.ts)
- ruleExecutionLoggingUtils.ts: [`server/services/analyticsLoggers/ruleExecutionLoggingUtils.ts`](https://github.com/roostorg/coop/blob/main/server/services/analyticsLoggers/ruleExecutionLoggingUtils.ts)
- conditionResults.ts: [`server/services/moderationConfigService/types/conditionResults.ts`](https://github.com/roostorg/coop/blob/main/server/services/moderationConfigService/types/conditionResults.ts)
- manualReviewToolService.ts: [`server/services/manualReviewToolService/manualReviewToolService.ts`](https://github.com/roostorg/coop/blob/main/server/services/manualReviewToolService/manualReviewToolService.ts)
- DecisionAnalytics.ts: [`server/services/manualReviewToolService/modules/DecisionAnalytics.ts`](https://github.com/roostorg/coop/blob/main/server/services/manualReviewToolService/modules/DecisionAnalytics.ts)

---

Part of [T&S Workbench](https://github.com/stevenmacchia/ts-workbench) by [Steven Macchia](https://www.linkedin.com/in/stevenmacchia). Content licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): reuse it freely with credit.
