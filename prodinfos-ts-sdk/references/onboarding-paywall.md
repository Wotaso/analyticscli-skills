# Onboarding And Paywall Contract

Prodinfos has strong support for onboarding and paywall funnel analytics, but that only works reliably if instrumentation follows a strict event contract.

## Use The SDK Wrappers

Prefer these helpers over ad-hoc strings:

- `trackOnboardingEvent(...)`
- `createOnboardingTracker(...)`
- `trackPaywallEvent(...)`
- `trackOnboardingSurveyResponse(...)`

Available constants:

- `ONBOARDING_EVENTS`
- `PAYWALL_EVENTS`
- `PURCHASE_EVENTS`
- `ONBOARDING_SURVEY_EVENTS`

## Required Onboarding Events

| Event | When to send | Required properties |
| --- | --- | --- |
| `onboarding:start` | User starts onboarding flow | `onboardingFlowId`, `onboardingFlowVersion`, `isNewUser`, `appVersion` |
| `onboarding:step_view` | A distinct onboarding step becomes visible | flow props plus `stepKey`, `stepIndex`, `stepCount` |
| `onboarding:step_complete` | User completes a step action | flow props plus `stepKey`, `stepIndex`, `stepCount` |
| `onboarding:complete` | Onboarding ends successfully | flow props |
| `onboarding:skip` | User exits or skips onboarding | flow props |
| `onboarding:survey_response` | Survey answer captured | `surveyKey`, `questionKey`, `answerType`, `responseKey`, plus flow props |

## Required Paywall And Purchase Events

| Event | When to send | Required properties |
| --- | --- | --- |
| `paywall:shown` | Paywall is visible | `source`, `paywallId`, `fromScreen`, `appVersion` |
| `paywall:skip` | User dismisses or skips paywall | `source`, `paywallId`, `appVersion` |
| `purchase:started` | Purchase flow started | `source`, `paywallId`, `packageId`, `appVersion` |
| `purchase:success` | Purchase succeeded | `source`, `paywallId`, `packageId`, `appVersion` |
| `purchase:failed` | Purchase failed | `source`, `paywallId`, `packageId`, `appVersion` |
| `purchase:cancel` | In-app purchase cancel intent detected | `source`, `paywallId`, `packageId`, `appVersion` |

## Order Rules

Onboarding:

1. `onboarding:start`
2. `onboarding:complete` or `onboarding:skip`

Paywall journey:

1. `paywall:shown`
2. `purchase:started` optionally
3. `paywall:skip` or `purchase:success` or `purchase:failed`

## Example

```ts
import { PAYWALL_EVENTS, PURCHASE_EVENTS } from '@prodinfos/sdk-ts';

const onboarding = analytics.createOnboardingTracker({
  appVersion: '1.8.0',
  isNewUser: true,
  onboardingFlowId: 'onboarding_v4',
  onboardingFlowVersion: '4.0.0',
  stepCount: 5,
  surveyKey: 'onboarding_v4',
});
const welcomeStep = onboarding.step('welcome', 0);

onboarding.start();
welcomeStep.view();
welcomeStep.surveyResponse({
  questionKey: 'primary_goal',
  answerType: 'single_choice',
  responseKey: 'increase_revenue',
});

analytics.trackPaywallEvent(PAYWALL_EVENTS.SHOWN, {
  source: 'onboarding',
  paywallId: 'default_paywall',
  fromScreen: 'onboarding_offer',
  appVersion: '1.8.0',
});

analytics.trackPaywallEvent(PURCHASE_EVENTS.SUCCESS, {
  source: 'onboarding',
  paywallId: 'default_paywall',
  packageId: 'annual',
  appVersion: '1.8.0',
});
```

## Common Mistakes

- non-canonical names for core paywall or purchase milestones
- missing `onboardingFlowId` or `onboardingFlowVersion`
- missing `paywallId` or `source`
- mixing screen-view semantics with funnel milestones
