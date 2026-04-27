---
name: product-manager-skill
description: OpenClaw-first AI product manager for turning analytics, revenue, crash, store, and feedback signals into execution-ready proposals and backlog work.
license: MIT
homepage: https://github.com/wotaso/analyticscli-skills
metadata: {"author":"wotaso","version":"1.0.28","openclaw":{"emoji":"📌","homepage":"https://github.com/wotaso/analyticscli-skills","requires":{"bins":["node","analyticscli"]}}}
---

# AI Product Manager

## Use This Skill When

- you want OpenClaw to turn product signals into execution-ready backlog work
- you need one workflow across analytics, RevenueCat, Sentry/GlitchTip, feedback, store signals, and repo context
- you want the deterministic work to live in a standalone `openclaw` CLI and OpenClaw to stay the AI/chat layer
- you want delivery configurable between OpenClaw chat handoff, GitHub issues, and draft pull requests

## Preferred Runtime

Prefer the standalone `openclaw` CLI as the runtime surface.

- Setup path: `openclaw setup --config openclaw.config.json`
- Primary path: `openclaw start --config openclaw.config.json`
- Local monorepo path: `pnpm --filter @analyticscli/openclaw-cli dev -- start`
- Legacy copied-runtime scripts under `scripts/openclaw-growth-*.mjs` remain fallback-only for older OpenClaw workspaces

The CLI is intentionally non-AI. OpenClaw should stay the only conversational and implementation layer.
Use the CLI to gather signals, generate proposals, schedule checks, and send deliveries.
If the user later asks OpenClaw to implement a proposal, OpenClaw should inspect the generated drafts and then use OpenClaw itself to do the work.

## Customization Boundary

Treat this installed skill as vendor-managed and replaceable.
OpenClaw should almost never edit this skill in-place for user- or project-specific customization, because future ClawHub updates may overwrite local changes.
When the user wants custom behavior, create a separate companion skill or project-local customization skill instead, for example `openclaw-growth-custom`, and have that skill reference or layer on top of this one.
Only modify this skill directly when the change is intended as an upstream reusable fix for the canonical skill repository.

## Setup DX Rules

Setup should feel guided for a developer, not like a silent preflight dump.

- Prefer auto-detection and direct fixes over asking the user to run generic commands.
- Explain why each connection matters before asking for it, especially AnalyticsCLI auth, GitHub code access, and optional GitHub write scopes.
- Ask for the minimum missing value only; do not request issue/PR permissions unless artifact creation is enabled.
- For every blocker, return a compact checklist with status, why it matters, where to get it, and the exact minimum permission or command.
- After each setup phase, report what was detected, what was configured, and the next concrete command OpenClaw will run.
- Keep secrets out of prompts, repo files, logs, and command arguments; prefer OpenClaw secret storage or environment injection.
- When SDK instrumentation is missing or weak, guide the developer through the `analyticscli-ts-sdk` setup path so analytics events become useful for later growth analysis.
- If AnalyticsCLI has no default project and multiple projects are visible, do not report that as a hard error. List the available projects, ask the user which one to use, persist the choice with `openclaw start --config openclaw.config.json --project <project_id>` or `analyticscli projects select <project_id>`, and then retry the setup/run.

During setup, ask the user this concrete selection question before requesting optional credentials:

```text
Welche der folgenden Connections moechtest du aufsetzen? Mehrfachauswahl ist moeglich:
1. AnalyticsCLI analytics baseline
2. GitHub code access fuer Codeanalyse
3. ASC CLI fuer App Store Connect Analytics-Daten
4. RevenueCat fuer Monetization-/Subscription-Daten
5. Sentry/GlitchTip fuer Crash-/Error-Daten
6. Feedback/App Reviews
7. Erstmal ueberspringen
```

Then configure only the selected connections. Do not ask for all tokens at once.
For every selected connection, explain the minimum role/scope and exactly where the user finds the key or login flow.
If the user already says which connections they want, treat those as selected and start setup immediately. For example, "I want to connect ASC + codebase from GitHub" means configure ASC analytics data and GitHub code access; do not respond by asking for a repo path first, and do not claim ASC is connected merely because AnalyticsCLI works.

## Mandatory Baseline

Before autopilot runs, these are non-negotiable:

- `analyticscli` CLI available
- target repo checkout readable via `project.repoRoot`
- a writable `openclaw.config.json`
- `sources.analytics` enabled

GitHub connection is strongly recommended for serious analysis, even when GitHub delivery is disabled.
Treat readable GitHub repo access as very important because analytics signals become much more actionable when OpenClaw can map funnels, events, crashes, revenue signals, and feedback back to actual code areas.
Without repo context, findings stay generic and file/module hypotheses are lower confidence.

When the user says they want to connect GitHub or the codebase, do not ask them to manually send a repo path first.
Reference and use the dedicated ClawHub GitHub skill when available: `steipete/github` (`https://clawhub.ai/steipete/github`).
It is a `gh` CLI helper skill for issues, PRs, runs, and advanced `gh api` queries, so it should own GitHub command patterns while this skill owns product/growth analysis.
Install or verify it before deeper GitHub setup when OpenClaw can manage skills:

```bash
openclaw skills install steipete/github
# or
npx clawhub@latest install github
```

Start the GitHub CLI setup flow yourself:

1. Run `git rev-parse --show-toplevel` to detect the local repo root.
2. Run `git remote get-url origin` and infer `owner/repo` when possible.
3. Run `gh auth status`.
4. If `gh` is not authenticated, start `gh auth login` and tell the user to complete the browser/device flow.
5. After auth succeeds, use local repo context for read-only code analysis immediately.
6. Ask for issue or pull-request write permissions only if GitHub delivery is enabled.

Use the least privilege GitHub access that matches the requested workflow:

- code analysis only: readable repo/code access is enough; prefer `gh auth status` / `gh auth login` when an existing GitHub CLI login can be reused
- if the user must create a token, prefer a fine-grained read-only token with `Contents: Read` and `Metadata: Read`, and ask for access to all repositories only when the user wants cross-repo code analysis
- issue creation: add issue write permission only when GitHub issue delivery is enabled
- pull-request creation: add pull-request and contents write permission only when draft PR delivery is enabled

## Delivery Modes

The CLI can write proposals to one or more targets:

- `deliveries.openclawChat.enabled = true`: write `.openclaw/chat/latest.md` and `.openclaw/chat/latest.json` for OpenClaw to pick up in chat
- `deliveries.github.mode = "issue"` with `deliveries.github.autoCreate = true`: create implementation-ready GitHub issues
- `deliveries.github.mode = "pull_request"` with `deliveries.github.autoCreate = true`: create draft PRs that add `.openclaw/proposals/...md` proposal files to the repo

## Connector Model

Built-in channels:

- `analytics`
- `revenuecat`
- `sentry`
- `feedback`
  default command path: `analyticscli feedback summary --format json`
  default cursor behavior: first run `--last 30d`, later runs `--since <lastCollectedAt>` unless the command already sets explicit time flags

Additional connectors:

- configure `sources.extra[]`
- each extra connector can use `mode=file` or `mode=command`
- preferred output is shared `signals[]`
- crash-style tools may use `issues[]`
- feedback-style tools may use `items[]`

For iOS/macOS products, explicitly ask whether the user wants to connect the `asc` CLI and the related App Store Connect agent skill for App Store Connect Analytics data only.
Frame ASC as additive monthly analytics signal, not a hard blocker: AnalyticsCLI remains the baseline, while ASC Analytics reports can add App Store discovery, downloads, usage, purchase, and subscription context.
Do not request ASC permissions for release management, TestFlight management, pricing changes, user management, or other write/admin workflows when the user only selected analytics data.
Reference the ASC skill pack as the canonical companion skills for `asc`: `rorkai/app-store-connect-cli-skills` (`https://github.com/rorkai/app-store-connect-cli-skills`).
Use it for `asc` command syntax, auth, pagination, ID resolution, and App Store Connect workflows; for analytics-only setup prefer the least invasive skills such as `asc-cli-usage` and `asc-id-resolver`, not release/submission/signing skills.
Install or refresh it when the user selects ASC:

```bash
npx skills add rorkai/app-store-connect-cli-skills
```

ASC setup guidance:

- Ask: "Soll ASC CLI fuer App Store Connect Analytics-Daten verbunden werden?"
- Recommend the least-privilege App Store Connect API access that can read analytics reports: prefer a Sales/Sales and Reports style role for generated analytics reports; Finance can work but is broader; Admin should only be used temporarily when a new analytics report type must be requested for the first time.
- Prefer an individual API key for a user limited to the target app when possible; team API keys can cover all apps and are broader.
- Tell the user where to create the key: App Store Connect -> Users and Access -> Integrations -> App Store Connect API for team keys, or profile menu -> Edit Profile -> Individual API Key for individual keys.
- Store only env vars/secrets: `ASC_KEY_ID`, `ASC_ISSUER_ID`, and `ASC_PRIVATE_KEY` or `ASC_PRIVATE_KEY_PATH`; never commit the `.p8` private key.
- Prefer `asc auth login` when the local `asc` CLI supports keychain storage; otherwise use runtime env injection.

RevenueCat setup guidance:

- Ask: "Soll RevenueCat fuer Monetization-/Subscription-Daten verbunden werden?"
- For SDK instrumentation, use the public app-specific SDK key only in the app.
- For server-side growth summaries, request a RevenueCat secret API key stored server-side only. Prefer a v2 secret key with read-only permissions for charts/metrics and required project configuration resources such as apps, products, offerings, packages, and entitlements; add customer/subscriber read only if the selected summary needs it.
- Tell the user where to create it: RevenueCat Dashboard -> Project Settings -> API Keys -> + New secret API key.
- Store it as `REVENUECAT_API_KEY` in OpenClaw secret storage or runtime env; never put it in client code, config JSON, issues, or PR bodies.

## Feedback Rules

- Always include a stable `locationId` for feedback collection points
- Always include a human-readable `originName` for where the feedback originated in the product
- Prefer AnalyticsCLI feedback retrieval via `analyticscli feedback summary --format json` instead of maintaining a second feedback definition
- The SDK should track lightweight feedback submission events without sending raw feedback text into analytics events

## Feedback Source Memory

- The CLI should persist per-source cursor state, especially for the built-in `feedback` source
- Default behavior must avoid accidental historical re-fetches
- If `sources.feedback.cursorMode = "auto_since_last_fetch"` and the command has no explicit `--since`, `--until`, or `--last`, the CLI should auto-append a bounded window
- Re-fetching older history should always be a conscious action by changing the command or resetting cursor state

## Startup Protocol

When the user says `start`, `run`, or `kick off`:

1. Prefer the CLI entrypoint:
   - `openclaw setup --config openclaw.config.json`
2. Then run:
   - `openclaw start --config openclaw.config.json`
3. In this monorepo, use the workspace dev entrypoint when `openclaw` is not installed globally:
   - `pnpm --filter @analyticscli/openclaw-cli dev -- start`
4. Run portable checks first when setup is incomplete:
   - `command -v analyticscli`
   - `analyticscli projects list`
   - detect `project.githubRepo` from git remote when possible
   - verify readable GitHub repo access when available so analytics findings can be mapped to code
   - verify GitHub issue/PR write scopes only if GitHub delivery is enabled
5. If preflight fails, return only a concrete blocker checklist
6. If preflight passes, continue with `openclaw run --config openclaw.config.json`

## Proposal Strategy

The CLI config should expose `strategy.proposalMode`:

- `mandatory`: only strongest, clearly evidenced fixes and must-have requests
- `balanced`: default mix of necessary fixes and moderate product ideas
- `creative`: still evidence-led, but more willing to suggest bolder experiments or feature ideas

## Output Rules

- max 3-5 proposals per pass
- each proposal must include measurable impact and file/module hypotheses
- each proposal must say what should change
- low-confidence findings must be marked explicitly
- when GitHub delivery is disabled, proposals should still be fully usable via the OpenClaw chat outbox

## Required Secrets

- `GITHUB_TOKEN`
  strongly recommended with readable repo/code access for code-aware analysis
  required with write scopes only when GitHub issue or pull-request delivery is enabled
- `ASC_KEY_ID`, `ASC_ISSUER_ID`, `ASC_PRIVATE_KEY` or `ASC_PRIVATE_KEY_PATH`
  optional; ask before setup
  App Store Connect Analytics data only
  prefer Sales/Sales and Reports style access; Admin only temporarily for first-time report type requests
- `ANALYTICSCLI_READONLY_TOKEN`
  recommended
- `REVENUECAT_API_KEY`
  optional; ask before setup
  use a server-side secret API key for RevenueCat command/API mode
  prefer v2 read permissions for charts/metrics and required project configuration resources
- `SENTRY_AUTH_TOKEN`
  recommended for Sentry command/API mode
- optional connector-specific `secretEnv` per `sources.extra[]`

## References

- [README](README.md)
- [Setup And Scheduling](references/setup-and-scheduling.md)
- [Required Secrets](references/required-secrets.md)
- [Input Schema](references/input-schema.md)
- [Issue Template](references/issue-template.md)
