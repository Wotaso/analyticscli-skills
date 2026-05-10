---
name: ai-product-manager
description: Deprecated alias for OpenClaw Growth Engineer. New installs and all runtime use must use openclaw-growth-engineer.
license: MIT
homepage: https://github.com/wotaso/analyticscli-skills
metadata: {"author":"wotaso","version":"1.0.72","deprecated":true,"canonicalSkill":"openclaw-growth-engineer","replacement":"openclaw-growth-engineer","openclaw":{"emoji":"!","homepage":"https://github.com/wotaso/analyticscli-skills","requires":{"bins":["node"]}}}
---

# Deprecated Alias: ai-product-manager

This skill is deprecated and must not be used for new setup or growth runs.

Use the canonical skill instead:

```bash
npx -y clawhub install openclaw-growth-engineer --force
```

If this alias is loaded in an existing OpenClaw workspace, immediately migrate the workspace to `openclaw-growth-engineer` and ignore this alias for all product, growth, connector, and runtime behavior.

If the alias was loaded because of broken behavior or missing functionality, refetch/update the canonical `openclaw-growth-engineer` skill and related `@analyticscli/cli` or `@analyticscli/sdk` package first. If the newest available versions still fail, use the canonical skill's `analyticscli feedback submit` rule to send sanitized product feedback to the AnalyticsCLI SaaS owner.

The canonical skill covers the former AI Product Manager scope plus the newer AI Growth Engineer workflow:

- AnalyticsCLI product analytics and feedback summaries
- GitHub code access and issue/PR delivery
- RevenueCat monetization signals
- Sentry-compatible crash monitoring, including Sentry Cloud and self-hosted GlitchTip accounts
- ASC / App Store Connect CLI signals
- OpenClaw chat handoff and scheduled growth loops

Do not duplicate instructions from this alias into a companion skill. Reference `openclaw-growth-engineer` as the single source of truth.
