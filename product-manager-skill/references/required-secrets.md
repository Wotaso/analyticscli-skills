# Required Secrets

Use this checklist before running autopilot mode.

## Secret Inventory

| Env var | Purpose | Required | Where to get it |
| --- | --- | --- | --- |
| `GITHUB_TOKEN` | GitHub repo/code access for analysis; optional issue/PR creation via API | Strongly recommended for code-aware analysis; required only for GitHub issue/PR creation | GitHub -> Settings -> Developer settings -> Fine-grained PAT |
| `ANALYTICSCLI_ACCESS_TOKEN` | Read analytics data with CLI commands | Recommended | [dash.analyticscli.com](https://dash.analyticscli.com) -> API Keys -> access token |
| `REVENUECAT_API_KEY` | Pull RevenueCat monetization data | Recommended | RevenueCat -> Project -> API Keys (Secret key) |
| `SENTRY_AUTH_TOKEN` | Pull Sentry issue/event summaries | Recommended | Sentry -> User Settings -> Auth Tokens |
| `FEEDBACK_API_TOKEN` | Protect optional public feedback endpoint | Optional | Generate locally, e.g. `openssl rand -hex 32` |

## Minimum Scopes

- `GITHUB_TOKEN`:
  - Fine-grained PAT is enough (no full/account-wide token required)
  - Analysis only: repository access to the target repo with `Contents`: Read
  - Issue creation: add repository `Issues`: Read and Write only when issue delivery is enabled
  - Pull-request creation: add repository `Pull requests`: Read and Write and `Contents`: Read and Write only when draft PR delivery is enabled
- `ANALYTICSCLI_ACCESS_TOKEN`:
  - `read:queries` access for analytics CLI reads and exports
- `REVENUECAT_API_KEY`:
  - Read-only access where supported
- `SENTRY_AUTH_TOKEN`:
  - Read scopes for issues/events/projects in the target org/project

## Red Lines

- Never commit secrets to git.
- Never store secrets in `data/openclaw-growth-engineer/config.json`.
- Never pass secrets in CLI arguments.
- Never print full secrets in logs.
