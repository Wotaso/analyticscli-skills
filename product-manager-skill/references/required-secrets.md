# Required Secrets

Use this checklist before running autopilot mode.

## Secret Inventory

| Env var | Purpose | Required | Where to get it |
| --- | --- | --- | --- |
| `GITHUB_TOKEN` | GitHub repo/code access for analysis; optional issue/PR creation via API | Strongly recommended for code-aware analysis; required only for GitHub issue/PR creation | Prefer `gh auth login` when available. Token fallback: GitHub -> Settings -> Developer settings -> Fine-grained PAT |
| `ANALYTICSCLI_ACCESS_TOKEN` | Read analytics data with CLI commands | Recommended | [dash.analyticscli.com](https://dash.analyticscli.com) -> API Keys -> access token |
| `ASC_KEY_ID` / `ASC_ISSUER_ID` / `ASC_PRIVATE_KEY` | Pull App Store Connect Analytics reports through `asc` CLI | Optional, ask before setup | App Store Connect -> Users and Access -> Integrations -> App Store Connect API, or profile -> Edit Profile -> Individual API Key |
| `REVENUECAT_API_KEY` | Pull RevenueCat monetization/subscription data | Optional, ask before setup | RevenueCat -> Project Settings -> API Keys -> + New secret API key |
| `SENTRY_AUTH_TOKEN` | Pull Sentry issue/event summaries | Recommended | Sentry -> User Settings -> Auth Tokens |
| `FEEDBACK_API_TOKEN` | Protect optional public feedback endpoint | Optional | Generate locally, e.g. `openssl rand -hex 32` |

## Minimum Scopes

- `GITHUB_TOKEN`:
  - Fine-grained PAT is enough (no full/account-wide token required)
  - Prefer reusing an existing GitHub CLI login via `gh auth status`; if a token is needed, use fine-grained read-only access
  - Analysis only: repository access with `Contents`: Read and `Metadata`: Read; request all repositories only for cross-repo code analysis
  - Issue creation: add repository `Issues`: Read and Write only when issue delivery is enabled
  - Pull-request creation: add repository `Pull requests`: Read and Write and `Contents`: Read and Write only when draft PR delivery is enabled
- `ASC_*`:
  - Analytics data only; do not request release, TestFlight, pricing, user-management, or write/admin permissions for this connector
  - Prefer Sales/Sales and Reports style access for generated analytics reports; Finance is broader; Admin should only be temporary when a new analytics report type must be requested first
- `ANALYTICSCLI_ACCESS_TOKEN`:
  - `read:queries` access for analytics CLI reads and exports
- `REVENUECAT_API_KEY`:
  - Use a server-side secret key, never a client SDK key
  - Prefer RevenueCat v2 read permissions for charts/metrics and required project configuration resources; add customer/subscriber read only when needed by the selected summary
- `SENTRY_AUTH_TOKEN`:
  - Read scopes for issues/events/projects in the target org/project

## Red Lines

- Never commit secrets to git.
- Never store secrets in `data/openclaw-growth-engineer/config.json`.
- Never pass secrets in CLI arguments.
- Never print full secrets in logs.
