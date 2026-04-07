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
| `analyticscli-cli`    | Query analytics, validate instrumentation, export bounded data        | `@analyticscli/cli` `^0.1.0`                   |
| `analyticscli-ts-sdk` | Integrate or upgrade the JS/TS SDK in web, React Native, or Expo apps | `@analyticscli/sdk` `>=0.1.0-preview.0 <0.2.0` |

## Product Manager Skill

The PM/autopilot skill is distributed from its dedicated repository:

```bash
npx skills add Wotaso/ai-product-manager-skill
```

## ClawHub

For ClawHub users, the canonical published skill is:

```bash
npx -y clawhub install ai-product-manager
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
