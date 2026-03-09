# Store Review Playbook

## Goal

Measure where review prompts are shown and which placements correlate with positive outcomes.

## Recommended Events

- `review_prompt:eligible`
- `review_prompt:shown`
- `review_prompt:requested`
- `review_prompt:result`

## Recommended Properties

- `screen`
- `trigger`
- `fromScreen`
- `paywallId`
- `sessionCount`
- `daysSinceInstall`
- `hasPurchased`
- `plan`
- `appVersion`
- `platform`

## Key Questions

- Which screen has the best review completion rate?
- Does prompting near paywall help or hurt purchase conversion?
- Which trigger performs best by country or appVersion?

## CLI Queries

Completion after shown:

```bash
prodinfos conversion-after --project <id> --from review_prompt:shown --to review_prompt:result --last 30d
```

Breakdown by screen:

```bash
prodinfos breakdown --project <id> --type conversion_after --from review_prompt:shown --to review_prompt:result --by screen --last 30d
```
