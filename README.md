# Prodinfos Skills

Public Agent Skills pack for Prodinfos.

This repository is intended to be mirrored from the private product monorepo and consumed by Agent Skills-compatible clients such as Codex, Claude Code, and OpenClaw.

## Install

Install the analytics/query skill:

```bash
npx skills add wotaso/prodinfos-skills --skill prodinfos-cli
```

Install the TypeScript SDK integration skill:

```bash
npx skills add wotaso/prodinfos-skills --skill prodinfos-ts-sdk
```

If you want both, run both commands.

For OpenClaw via ClawHub:

```bash
npx -y clawhub install prodinfos-cli
npx -y clawhub install prodinfos-ts-sdk
```

## Included Skills

| Skill | Use it for | Target package |
| --- | --- | --- |
| `prodinfos-cli` | Query analytics, validate instrumentation, export bounded data | `@prodinfos/cli` `^0.1.0` |
| `prodinfos-ts-sdk` | Integrate or upgrade the JS/TS SDK in web, React Native, or Expo apps | `@prodinfos/sdk-ts` `>=0.1.0-preview.0 <0.2.0` |

## Repo Layout

```text
.
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── prodinfos-cli/
│   ├── SKILL.md
│   └── references/
└── prodinfos-ts-sdk/
    ├── SKILL.md
    └── references/
```

## Versioning Policy

- `metadata.version` inside a `SKILL.md` is the version of the skill instructions.
- The supported product version range belongs in the body and metadata for the target package, not in the skill name by default.
- The unversioned skill names track the current stable line.
- If a future major release needs materially different instructions, add a sibling skill such as `prodinfos-ts-sdk-v1` instead of mixing incompatible majors into one file.
- Use Git tags and release notes on this public repo for distribution history.

## OpenClaw

The same skill folders work for OpenClaw because they follow the Agent Skills `SKILL.md` format.
You do not need a second skill format for OpenClaw.

If you also want store-style discovery or versioned bundle distribution inside the OpenClaw ecosystem, publish the same folders to ClawHub as an additional channel.

Sync all skills to ClawHub from the monorepo:

```bash
pnpm skills:sync:clawhub -- --all --tags latest
```

## Support Model

- Open issues in the public repo for install problems or unclear instructions.
- Product code still lives in the main Prodinfos monorepo.
- Mirror pushes can overwrite direct edits in the public repo, so discuss larger changes before opening a PR.
