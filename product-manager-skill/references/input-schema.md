# Input Schema (MVP)

The analyzer accepts flexible JSON, but this shape is recommended for reliable issue quality.

## `analytics_summary.json` (required)

```json
{
  "project": "my-product",
  "window": "last_30d",
  "signals": [
    {
      "id": "retention_d3_drop",
      "title": "Day-3 retention dropped after onboarding paywall changes",
      "area": "onboarding",
      "priority": "high",
      "metric": "d3_retention",
      "current_value": 0.18,
      "baseline_value": 0.27,
      "delta_percent": -33.3,
      "evidence": [
        "Drop started after release 1.4.2",
        "Largest loss between onboarding step 2 and paywall view"
      ],
      "suggested_actions": [
        "Move paywall after first core value event",
        "Simplify onboarding step 2 form"
      ],
      "keywords": ["onboarding", "paywall", "trial"]
    }
  ]
}
```

## `revenuecat_summary.json` (recommended)

```json
{
  "signals": [
    {
      "id": "trial_to_paid_down",
      "title": "Trial-to-paid conversion dropped in weekly package",
      "area": "paywall",
      "priority": "high",
      "metric": "trial_to_paid",
      "current_value": 0.08,
      "baseline_value": 0.12,
      "delta_percent": -33.0,
      "evidence": ["Drop is strongest on iOS and onboarding entry paywall"],
      "keywords": ["subscription", "pricing", "trial", "weekly"]
    }
  ]
}
```

## `sentry_summary.json` (recommended)

```json
{
  "issues": [
    {
      "id": "sentry_1431",
      "title": "TypeError in paywall purchase callback",
      "priority": "high",
      "impact": "Conversion blocker in purchase flow",
      "events": 312,
      "users": 119,
      "stack_keywords": ["paywall", "purchase", "subscription", "callback"],
      "evidence": ["Crash occurs within 3s after paywall shown"]
    }
  ]
}
```

## `feedback_summary.json` (optional)

```json
{
  "window": "last_30d",
  "items": [
    {
      "id": "fb_onboarding_too_long",
      "title": "Onboarding feels too long before first value",
      "area": "onboarding",
      "priority": "medium",
      "count": 14,
      "channel": "support_tickets",
      "comment": "Users ask for a faster path to first result",
      "locations": [
        { "location_id": "onboarding/profile_step", "count": 9 },
        { "location_id": "onboarding/permissions_gate", "count": 5 }
      ],
      "keywords": ["onboarding", "friction", "first value"]
    }
  ]
}
```

## `sources.extra[]` connectors

Extra connectors should prefer the shared `signals[]` shape.
Crash-style tools may use `issues[]`; feedback/review tools may use `items[]`.

## Multiple Sentry-Compatible Accounts

Use `sources.sentry.accounts[]` when an app has more than one Sentry-compatible crash source, for example Sentry Cloud plus a self-hosted GlitchTip instance:

```json
{
  "sources": {
    "sentry": {
      "enabled": true,
      "mode": "command",
      "command": "node scripts/export-sentry-summary.mjs --config data/openclaw-growth-engineer/config.json",
      "accounts": [
        {
          "id": "sentry_cloud",
          "label": "Sentry Cloud",
          "baseUrl": "https://sentry.io",
          "tokenEnv": "SENTRY_AUTH_TOKEN",
          "org": "owner-org",
          "projects": ["ios-app"],
          "environment": "production"
        },
        {
          "id": "glitchtip_selfhosted",
          "label": "GlitchTip self-hosted",
          "baseUrl": "https://glitchtip.example.com",
          "tokenEnv": "GLITCHTIP_AUTH_TOKEN",
          "org": "owner-org",
          "projects": ["backend-api", "web-app"],
          "environment": "production"
        }
      ]
    }
  }
}
```

Rules:

- keep tokens in environment variables or the OpenClaw secret file, not in config
- set one `tokenEnv` per account when Sentry Cloud and GlitchTip use different credentials
- use `projects[]` for all projects in that account that can affect the same product/app
- the exporter emits one combined `sentry:multiple` summary when more than one account/project is configured
