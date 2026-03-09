# Event Placement Playbook

## Goal

Track only high-leverage product moments so analytics stays actionable and stable.

## Must-Track Moments

- `onboarding:start`
- `onboarding:complete`
- `activation:first_value`
- `paywall:shown`
- `purchase:success`
- `review_prompt:shown`
- `feedback_submitted`

## Dedicated Events: Onboarding to Paywall to Subscription

- `onboarding:start`
- `onboarding:step_view`
- `onboarding:step_complete`
- `onboarding:complete`
- `onboarding:skip`
- `paywall:shown`
- `paywall:skip`
- `purchase:started`
- `purchase:success`
- `purchase:failed`
- `purchase:cancel`

## Placement Rules

- Track at intent boundaries, not every UI tap.
- Emit one event per semantic action.
- Use stable names and avoid silent renames.
- Attach compact, query-relevant properties only.
- Avoid direct PII fields.

## Required Properties For Funnel Stability

- `fromScreen`
- `appVersion`
- `platform`
- `runtimeEnv`
- `isNewUser`
- `onboardingFlowId`
- `onboardingFlowVersion`

Step-level properties:

- `stepKey`
- `stepIndex`
- `stepCount`

Paywall and purchase properties:

- `paywallId`
- `experimentVariant`
- `plan`
- `productId`
- `currency`

## Dashboard Rules For Dedicated Bar Chart

- Denominator: distinct users with `isNewUser=true` who triggered `onboarding:start`
- Numerator per bar: distinct users from the same cohort who triggered the target event
- Always segment by `onboardingFlowId`
- Do not mix flows with different `stepCount` in one linear step chart

## Validation Loop

```bash
prodinfos schema events --project <id> --limit 200
prodinfos goal-completion --project <id> --start onboarding:start --complete onboarding:complete --last 30d
prodinfos goal-completion --project <id> --start onboarding:start --complete purchase:success --last 30d
prodinfos goal-completion --project <id> --start paywall:shown --complete paywall:skip --last 30d
```
