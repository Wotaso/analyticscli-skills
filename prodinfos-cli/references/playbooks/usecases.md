# CLI Use Cases Playbook

## Goal

Provide copy-paste analytics questions for common product decisions using `prodinfos` CLI.

## Query Discipline

- Always include `--project <id>`.
- Always include `--last <duration>` unless explicit `since/until` is needed.
- Use release-only data by default.

## Quick Health Checks

Project list:

```bash
prodinfos projects list
```

Known events and properties:

```bash
prodinfos schema events --project <id> --limit 200
```

## New Users And Activation

How many new users in the last 14 days:

```bash
prodinfos conversion-after --project <id> --from onboarding:start --to onboarding:complete --within user --last 14d --format json | jq '.totalFrom'
```

How many of those completed onboarding:

```bash
prodinfos goal-completion --project <id> --start onboarding:start --complete onboarding:complete --within user --last 14d --format text
```

How many converted from onboarding start to purchase:

```bash
prodinfos goal-completion --project <id> --start onboarding:start --complete purchase:success --within user --last 30d --format text
```

## Active Users And Trends

Daily active users for the last 14 days:

```bash
prodinfos timeseries --project <id> --metric unique_users --interval 1d --last 14d --viz table
```

Hourly active users for the last 48 hours:

```bash
prodinfos timeseries --project <id> --metric unique_users --interval 1h --last 48h --viz table
```

Daily session trend:

```bash
prodinfos timeseries --project <id> --metric unique_sessions --interval 1d --last 30d --viz chart
```

Event volume trend for onboarding starts:

```bash
prodinfos timeseries --project <id> --metric event_count --event onboarding:start --interval 1d --last 30d --viz table
```

Timeseries trend delta from window start to now:

```bash
prodinfos timeseries --project <id> --metric unique_users --interval 1d --last 30d --trend --format json
```

## Funnel And Dropoff

Onboarding funnel dropoff:

```bash
prodinfos funnel --project <id> --steps onboarding:start,onboarding:step_complete,onboarding:complete --within user --last 30d
```

Paywall-to-purchase funnel:

```bash
prodinfos funnel --project <id> --steps paywall:shown,purchase:started,purchase:success --within user --last 30d
```

Most common next events after onboarding start:

```bash
prodinfos paths-after --project <id> --from onboarding:start --within user --top 20 --last 30d
```

## Segmentation And Breakdown

Top countries for onboarding starts:

```bash
prodinfos breakdown --project <id> --type event_count --event onboarding:start --by country --top 15 --last 30d
```

Onboarding completion by app version:

```bash
prodinfos breakdown --project <id> --type conversion_after --from onboarding:start --to onboarding:complete --by appVersion --within user --top 20 --last 30d
```

Purchase conversion by paywall ID:

```bash
prodinfos breakdown --project <id> --type conversion_after --from paywall:shown --to purchase:success --by paywallId --within user --top 20 --last 30d
```

## Onboarding Survey

Survey responses by question and answer:

```bash
prodinfos survey --project <id> --event onboarding:survey_response --last 30d --top-questions 12 --top-answers 8 --min-users 3 --format text
```

Single-question breakdown:

```bash
prodinfos survey --project <id> --question-key primary_goal --last 30d --min-users 5 --format text
```

Flow-specific survey analysis:

```bash
prodinfos survey --project <id> --survey-key onboarding_v4 --flow-id onboarding_v4 --flow-version 4.1.0 --last 30d --min-users 5 --format text
```

Most frequent survey questions:

```bash
prodinfos breakdown --project <id> --type event_count --event onboarding:survey_response --by questionKey --top 20 --last 30d
```

Most frequent survey answers:

```bash
prodinfos breakdown --project <id> --type event_count --event onboarding:survey_response --by responseKey --top 25 --last 30d
```

Survey answer shares as percentages per question:

```bash
prodinfos survey --project <id> --event onboarding:survey_response --last 30d --top-questions 12 --top-answers 8 --min-users 3 --format json | jq '.questions[] | {questionKey, answers: [.answers[] | {responseKey, percentage: (.share * 100)}]}'
```

## Retention And Churn

Retention D1, D7, D30 for onboarding cohort:

```bash
prodinfos retention --project <id> --anchor-event onboarding:start --days 1,7,30 --last 60d --max-age-days 90 --format text
```

Retention D1, D3, D7, D14, D30 for install cohort:

```bash
prodinfos retention --project <id> --anchor-event app:installed --days 1,3,7,14,30 --last 90d --max-age-days 120 --format text
```

Retention based only on a core activity event:

```bash
prodinfos retention --project <id> --anchor-event onboarding:start --active-event session:started --days 1,7,30 --last 60d --max-age-days 90 --format text
```

30-day retention by onboarding variant:

```bash
prodinfos retention --project <id> --anchor-event onboarding:start --days 30 --last 90d --variant A --format text
prodinfos retention --project <id> --anchor-event onboarding:start --days 30 --last 90d --variant B --format text
```

Churn signal after paywall exposure:

```bash
prodinfos retention --project <id> --anchor-event paywall:shown --days 1,3,7,14 --last 60d --max-age-days 90 --format text
```

## Onboarding And Paywall Snapshot

One-command snapshot:

```bash
prodinfos get onboarding-journey --project <id> --within user --last 30d --format text
```

Same snapshot plus trends:

```bash
prodinfos get onboarding-journey --project <id> --within user --last 30d --with-trends --format json
```

Flow-specific snapshot:

```bash
prodinfos get onboarding-journey --project <id> --within user --last 30d --flow-id onboarding_v3 --flow-version 3.2.0 --variant B --paywall-id main_paywall --format text
```

Paywall skip rate per paywall:

```bash
prodinfos breakdown --project <id> --type conversion_after --from paywall:shown --to paywall:skip --by paywallId --within user --top 20 --last 30d
```

Purchase conversion per onboarding flow:

```bash
prodinfos breakdown --project <id> --type conversion_after --from onboarding:start --to purchase:success --by onboardingFlowId --within user --top 20 --last 30d
```

## Debug And Data Quality

Run a query including debug or dev events:

```bash
prodinfos timeseries --project <id> --metric unique_users --interval 1d --last 7d --include-debug --viz table
```

## Reusable Reporting Exports

Save DAU trend as SVG:

```bash
prodinfos timeseries --project <id> --metric unique_users --interval 1d --last 30d --viz svg --out ./dau-30d.svg
```

List available export months:

```bash
prodinfos events months --project <id> --year 2026
```

Export one month of events as CSV:

```bash
prodinfos events export --project <id> --year 2026 --month 2 --out ./events-2026-02.csv
```
