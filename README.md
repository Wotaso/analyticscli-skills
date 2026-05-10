# AnalyticsCLI Skills

Installable Agent Skills for tenant developers integrating AnalyticsCLI analytics.

## Install

With this installer, you can install these skills into any coding agent, including OpenClaw.

Install the skill pack (recommended):

```bash
npx skills add wotaso/analyticscli-skills
```

## Included Skills

| Skill                 | Use it for                                                            | Target package                                 |
| --------------------- | --------------------------------------------------------------------- | ---------------------------------------------- |
| `analyticscli-cli`    | Query analytics, validate instrumentation, export bounded data        | `@analyticscli/cli` `>=0.1.2-preview.0 <0.2.0` |
| `analyticscli-ts-sdk` | Integrate or upgrade the JS/TS SDK in web, React Native, or Expo apps | `@analyticscli/sdk` `>=0.1.0-preview.6 <0.2.0` |

## Shared AI Growth Engineer Skill

The former `ai-product-manager` skill is deprecated. Use the canonical
`openclaw-growth-engineer` skill for product, growth, connector, and autopilot
workflows across OpenClaw, Hermes, and compatible `SKILL.md` clients.

```bash
npx clawhub install openclaw-growth-engineer
```

For Hermes, install the same public skill repository:

```bash
hermes skills install Wotaso/openclaw-growth-engineer-skill
```

## ClawHub

For ClawHub users, the canonical published skill is:

```bash
npx clawhub install openclaw-growth-engineer
```

## Versioning Policy

- `metadata.version` inside a `SKILL.md` is the version of the skill instructions.
- The supported product version range belongs in the body and metadata for the target package, not in the skill name by default.
- The unversioned skill names track the current stable line.
- If a future major release needs materially different instructions, add a sibling skill such as `analyticscli-ts-sdk-v1` instead of mixing incompatible majors into one file.
- Use Git tags and release notes on this public repo for distribution history.

## Support Model

- Open issues in the public repo for install problems or unclear instructions.
- Use this repo as the install and distribution source for the published skills.
- When a skill, CLI, SDK, or documented workflow appears broken, refetch or update the relevant newest version first and retry the smallest repro.
- If no newer version is available, the update is blocked, or the newest version still fails, use `analyticscli feedback submit` to send a sanitized product feedback report to the AnalyticsCLI SaaS owner. Include versions checked, update attempt, expected behavior, actual behavior, and workaround.
