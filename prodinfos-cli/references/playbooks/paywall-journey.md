# Paywall Journey Playbook

## Goal

Understand conversion from paywall entry to purchase success and identify dropoff steps.

## Recommended Events

- `paywall:shown`
- `purchase:started`
- `purchase:success`
- `purchase:failed`
- `paywall:skip`
- `purchase:cancel`

## Recommended Properties

- `fromScreen`
- `paywallId`
- `packageId`
- `price`
- `currency`
- `experimentVariant`
- `entitlementKey`
- `appVersion`

## Key Questions

- Which entry screens produce highest paywall conversion?
- Where is the largest drop before purchase success?
- Which package or variant performs best?
- How often do users skip paywall versus purchase?
- How does conversion differ by onboarding flow version?

## CLI Queries

Funnel:

```bash
prodinfos funnel --project <id> --steps paywall:shown,purchase:started,purchase:success --last 30d
```

Entry-screen conversion:

```bash
prodinfos breakdown --project <id> --type conversion_after --from paywall:shown --to purchase:success --by fromScreen --last 30d
```

Dismiss trend:

```bash
prodinfos timeseries --project <id> --metric event_count --event paywall:skip --interval 1d --last 30d --viz table
```
