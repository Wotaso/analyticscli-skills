# Onboarding And Paywall Contract

AnalyticsCLI has strong support for onboarding and paywall funnel analytics, but that only works reliably if instrumentation follows a strict event contract.

Credential source reminder:
- Get publishable ingest API key and optional CLI `readonly_token` from project **API Keys** in [dash.analyticscli.com](https://dash.analyticscli.com).
- Optional for CLI verification: set a default project once with `analyticscli projects select` (arrow-key picker), or pass `--project <project_id>` per command.

## Use The SDK Wrappers

Prefer these helpers over ad-hoc strings:

- `trackOnboardingEvent(...)`
- `createOnboardingTracker(...)`
- `createPaywallTracker(...)`
- `trackPaywallEvent(...)`
- `trackOnboardingSurveyResponse(...)`

Available constants:

- `ONBOARDING_EVENTS`
- `PAYWALL_EVENTS`
- `PURCHASE_EVENTS`
- `ONBOARDING_SURVEY_EVENTS`

## Host App Shape

Use a thin host integration:

- one SDK bootstrap (`init('<APP_KEY>')`, `init({...})`, or `initFromEnv(...)`)
- direct tracker/event calls in feature code
- minimal shared helpers only when multiple call sites truly reuse the same payload shape

Avoid:

- giant event translation files
- keeping old event aliases forever
- generic `trackEvent(...)` proxies that hide canonical SDK APIs

## Default Migration Policy For Existing Event Names

When an implementation touches paywall/purchase tracking in an app that still has legacy custom events:

- migrate touched call sites to canonical names in the same change by default
- do not leave legacy names as the primary instrumentation for those flows
- use temporary dual-write only when explicitly requested or when a short validation window is required
- if dual-write is used, add a clear removal follow-up and keep the window time-boxed

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

If exposed by your paywall provider, include `offering` in tracker defaults:
- RevenueCat: offering identifier
- Adapty: paywall/placement identifier
- Superwall: placement/paywall identifier

## Duplicate Tracking Prevention

- SDK built-in dedupe is scoped to `onboarding:step_view` only and is enabled by default (`dedupeOnboardingStepViewsPerSession: true`).
- SDK does not automatically dedupe paywall, purchase, or `screen:*` events.
- Assign a single owner for each funnel boundary (route-level or component-level, not both).
- Do not track the same screen transition from both parent layout and child screen hooks.
- For each paywall attempt, emit one `paywall:shown`.
- For each purchase attempt, emit one `purchase:started` and exactly one terminal event:
  - `purchase:cancel`
  - `purchase:failed`
  - `purchase:success`
- Use `createPaywallTracker(...)` so events share one `paywallEntryId`; this improves correlation and duplicate detection in analysis, but it does not dedupe automatically.
- Reuse a single `createPaywallTracker(...)` instance for one stable paywall flow context. Do not recreate a tracker for every event callback.
- Include provider offering/paywall identifier as `offering` in tracker defaults when available.
- If multiple callbacks can fire during re-render/re-mount, gate emissions with a session-local idempotency key.

## Screen View Coverage

Track screen views for all funnel-relevant screens:

- onboarding steps and onboarding completion/skip screens
- paywall screen
- purchase result and restore result screens
- core feature entry screens (where value creation starts)

Recommended approach:

- Prefer `analytics.screen('<screen_name>', props)` for new integrations.
- If your app already uses `screen_view`, keep that naming only during migration and standardize afterwards.
- Include stable fields: `screen_name`, `screen_class`, `source`, `appVersion`, `platform`.
- Prefer direct canonical calls (`trackPaywallEvent`, tracker methods) at call sites over generic `trackEvent(...)` proxy layers.

## Important Product Action Events

Beyond funnel milestones, add events for high-value functionality that signals activation or retained usage.

| Event | When to send | Suggested properties |
| --- | --- | --- |
| `activation:first_value` | First successful core value action | `source`, `appVersion`, `platform` |
| `calibration:completed` | Calibration finished successfully | `method`, `referenceWidthMm`, `appVersion` |
| `result:generated` | Ring size result computed | `inputMode`, `region`, `appVersion` |
| `result:shared` | Result shared/exported | `channel`, `source`, `appVersion` |
| `restore:started` | Restore purchases initiated | `source`, `appVersion` |
| `restore:completed` | Restore flow completed | `source`, `restoredEntitlements`, `appVersion` |
| `restore:failed` | Restore flow failed | `source`, `errorCode`, `appVersion` |

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
const onboarding = analytics.createOnboardingTracker({
  appVersion: '1.8.0',
  isNewUser: true,
  onboardingFlowId: 'onboarding_v4',
  onboardingFlowVersion: '4.0.0',
  stepCount: 5,
  surveyKey: 'onboarding_v4',
});
const paywall = analytics.createPaywallTracker({
  source: 'onboarding',
  paywallId: 'default_paywall',
  offering: 'rc_main', // RevenueCat example
  appVersion: '1.8.0',
});
const welcomeStep = onboarding.step('welcome', 0);

onboarding.start();
welcomeStep.view();
welcomeStep.surveyResponse({
  questionKey: 'primary_goal',
  answerType: 'single_choice',
  responseKey: 'increase_revenue',
});

paywall.shown({
  fromScreen: 'onboarding_offer',
});

paywall.purchaseSuccess({
  packageId: 'annual',
});
```

## Common Mistakes

- non-canonical names for core paywall or purchase milestones
- touching paywall/purchase instrumentation but leaving legacy custom names unchanged
- missing `onboardingFlowId` or `onboardingFlowVersion`
- missing `paywallId` or `source`
- omitting `offering` although the paywall provider exposes an offering/paywall id
- creating a new `createPaywallTracker(...)` instance for every paywall event call
- mixing screen-view semantics with funnel milestones
- instrumenting only onboarding/paywall while skipping core product value events
- keeping old analytics provider as primary after AnalyticsCLI migration
