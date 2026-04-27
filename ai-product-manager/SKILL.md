---
name: product-manager-skill
description: OpenClaw-first AI product manager for turning analytics, revenue, crash, store, and feedback signals into execution-ready proposals and backlog work.
license: MIT
homepage: https://github.com/wotaso/analyticscli-skills
metadata: {"author":"wotaso","version":"1.0.21","openclaw":{"emoji":"📌","homepage":"https://github.com/wotaso/analyticscli-skills","requires":{"bins":["node","analyticscli"]}}}
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

## Setup DX Rules

Setup should feel guided for a developer, not like a silent preflight dump.

- Prefer auto-detection and direct fixes over asking the user to run generic commands.
- Explain why each connection matters before asking for it, especially AnalyticsCLI auth, GitHub code access, and optional GitHub write scopes.
- Ask for the minimum missing value only; do not request issue/PR permissions unless artifact creation is enabled.
- For every blocker, return a compact checklist with status, why it matters, where to get it, and the exact minimum permission or command.
- After each setup phase, report what was detected, what was configured, and the next concrete command OpenClaw will run.
- Keep secrets out of prompts, repo files, logs, and command arguments; prefer OpenClaw secret storage or environment injection.
- When SDK instrumentation is missing or weak, guide the developer through the `analyticscli-ts-sdk` setup path so analytics events become useful for later growth analysis.

## Mandatory Baseline

Before autopilot runs, these are non-negotiable:

- `analyticscli` CLI available
- target repo checkout readable via `project.repoRoot`
- a writable `openclaw.config.json`
- `sources.analytics` enabled

GitHub connection is strongly recommended for serious analysis, even when GitHub delivery is disabled.
Treat readable GitHub repo access as very important because analytics signals become much more actionable when OpenClaw can map funnels, events, crashes, revenue signals, and feedback back to actual code areas.
Without repo context, findings stay generic and file/module hypotheses are lower confidence.

Use the least privilege GitHub access that matches the requested workflow:

- code analysis only: readable repo/code access is enough
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
  required only when GitHub issue or pull-request delivery is enabled
- `ANALYTICSCLI_READONLY_TOKEN`
  recommended
- `REVENUECAT_API_KEY`
  recommended for RevenueCat command/API mode
- `SENTRY_AUTH_TOKEN`
  recommended for Sentry command/API mode
- optional connector-specific `secretEnv` per `sources.extra[]`

## References

- [README](README.md)
- [Setup And Scheduling](references/setup-and-scheduling.md)
- [Required Secrets](references/required-secrets.md)
- [Input Schema](references/input-schema.md)
- [Issue Template](references/issue-template.md)
