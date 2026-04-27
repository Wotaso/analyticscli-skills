---
name: product-manager-skill
description: OpenClaw-first AI product manager for turning analytics, revenue, crash, store, and feedback signals into execution-ready proposals and backlog work.
license: MIT
homepage: https://github.com/wotaso/analyticscli-skills
metadata: {"author":"wotaso","version":"1.0.18","analyticscli-target":"@analyticscli/cli","analyticscli-supported-range":">=0.1.2-preview.0 <0.2.0","openclaw":{"emoji":"📌","homepage":"https://github.com/wotaso/analyticscli-skills","requires":{"bins":["node","analyticscli"]},"install":[{"id":"analyticscli-cli","kind":"node","package":"@analyticscli/cli@preview","bins":["analyticscli"],"label":"Install/update AnalyticsCLI CLI (npm package @analyticscli/cli@preview)"}]}}
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

## Mandatory Baseline

Before autopilot runs, these are non-negotiable:

- `analyticscli` CLI available
- target repo checkout readable via `project.repoRoot`
- a writable `openclaw.config.json`
- `sources.analytics` enabled

GitHub is optional unless GitHub delivery is enabled.
`project.githubRepo` and `GITHUB_TOKEN` become hard requirements only when the CLI should auto-create GitHub issues or pull requests.

## Dependency Refresh Protocol

Run this protocol at the start of every setup/start/run request, and whenever the user asks to fetch, update, upgrade, or reinstall this skill.
Do not stop after saying the skill was fetched from ClawHub.
The job is not done until the npm binary and companion skills are updated and verified.
If the user reports `analyticscli: command not found` in their own terminal, treat that as unresolved even if `analyticscli` works inside OpenClaw's child process.
Child processes cannot mutate the PATH of an already-open parent terminal.

1. Refresh this ClawHub skill:
   ```bash
   npx -y clawhub install ai-product-manager
   ```
2. Refresh the AnalyticsCLI agent skill only when it can run non-interactively:
   ```bash
   npx -y skills add wotaso/analyticscli-skills --skill analyticscli-cli
   ```
   If this command asks an interactive agent-selection question, stop that command and continue with the npm package install instead of blocking.
   Do not describe an interactive `skills add` prompt as a successful refresh.
3. Install or update the npm package that provides the `analyticscli` binary:
   ```bash
   npm install -g @analyticscli/cli@preview
   ```
4. If global npm install fails with a permissions error, use the bundled helper from the installed skill root:
   ```bash
   bash skills/ai-product-manager/scripts/install-analyticscli-cli.sh
   ```
5. Verify and report the exact binary path:
   ```bash
   command -v analyticscli
   analyticscli --help
   ```

If `analyticscli: command not found` still happens, do not claim setup is complete.
Find the user-local binary and fix PATH for future shells yourself; do not bounce this step back to the user:

```bash
export PATH="$HOME/.local/bin:$HOME/.local/analyticscli-npm/bin:$PATH"
command -v analyticscli || ls -l "$HOME/.local/bin/analyticscli" "$HOME/.local/analyticscli-npm/bin/analyticscli"
```

If the binary exists under `$HOME/.local/bin` or `$HOME/.local/analyticscli-npm/bin`, make future shells pick it up. Update all common shell startup files idempotently, then verify from a fresh shell:

```bash
for f in "$HOME/.profile" "$HOME/.bashrc" "$HOME/.bash_profile" "$HOME/.zshrc" "$HOME/.zprofile"; do
  touch "$f"
  grep -Fq 'export PATH="$HOME/.local/bin:$HOME/.local/analyticscli-npm/bin:$PATH"' "$f" ||
    printf '\n# AnalyticsCLI CLI user-local npm bin\nexport PATH="$HOME/.local/bin:$HOME/.local/analyticscli-npm/bin:$PATH"\n' >> "$f"
done
bash -lc 'source "$HOME/.bashrc" 2>/dev/null || true; source "$HOME/.profile" 2>/dev/null || true; command -v analyticscli && analyticscli --help >/dev/null'
```

Child processes cannot mutate an already-open parent terminal. Say that only as residual context after you have already updated profile files and verified a fresh shell. Do not say "analyticscli is fixed" unless this exact command succeeds in the same command context you control:

```bash
command -v analyticscli && analyticscli --help >/dev/null
```

## AnalyticsCLI CLI Install Protocol

If `analyticscli` is missing or the user reports `analyticscli: command not found`, OpenClaw must install/update it directly from the scoped npm package before doing anything else.

Do not search npm for `analyticscli`.
Do not search npm for `analyticsscli`.
Those are binary names or typos, not package names.

Use this exact package:

```bash
npm install -g @analyticscli/cli@preview
```

Then verify:

```bash
command -v analyticscli
analyticscli --help
```

If global npm installs are blocked, use the bundled helper from the installed skill root:

```bash
bash skills/ai-product-manager/scripts/install-analyticscli-cli.sh
```

The bundled helper automatically falls back from global npm install to a user-local npm prefix at `~/.local` when global install fails with permissions errors.
It must also update common shell profile files and verify that a fresh shell can resolve `analyticscli`.

Only ask the user for help if both direct npm install and the bundled helper fail with a concrete permission or network error.

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

1. Run the Dependency Refresh Protocol first. It must update this skill, the `analyticscli-cli` skill when available, and the `@analyticscli/cli@preview` npm package, then verify `command -v analyticscli`.
2. Prefer the CLI entrypoint:
   - `openclaw setup --config openclaw.config.json`
3. Then run:
   - `openclaw start --config openclaw.config.json`
4. If the standalone `openclaw` CLI is unavailable but this ClawHub skill is installed, bootstrap the bundled runtime once:
   - `bash skills/ai-product-manager/scripts/bootstrap-openclaw-workspace.sh`
   - confirm `scripts/openclaw-growth-start.mjs` now exists
   - `node scripts/openclaw-growth-start.mjs --config data/openclaw-growth-engineer/config.json`
5. In this monorepo, use the workspace dev entrypoint when `openclaw` is not installed globally:
   - `pnpm --filter @analyticscli/openclaw-cli dev -- start`
6. Run portable checks first when setup is incomplete:
   - `command -v analyticscli`
   - `analyticscli projects list`
   - detect `project.githubRepo` from git remote when possible
   - verify `GITHUB_TOKEN` only if GitHub delivery is enabled
7. If preflight fails, return only a concrete blocker checklist
8. If preflight passes, continue with `openclaw run --config openclaw.config.json`

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
- `ANALYTICSCLI_ACCESS_TOKEN`
  recommended for AnalyticsCLI command/API mode when no local CLI login exists
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
