---
name: internal-cli-dogfeeding
description: Internal maintainer playbook for AnalyticsCLI CLI dogfeeding telemetry in this monorepo. Not tenant-facing.
license: MIT
---

# Internal CLI Dogfeeding

## Scope

- This skill is for monorepo maintainers only.
- This is not part of tenant-facing setup or public onboarding.
- Goal: observe internal CLI usage quality (command start/success/failure, parse failures) without requiring collector write keys on user machines.

## Use Case

- Maintainers install and use `@analyticscli/cli` locally.
- CLI telemetry is opt-in via:
  - `ANALYTICSCLI_SELF_TRACKING_ENABLED=true`
- CLI sends telemetry to API route:
  - `POST /v1/telemetry/cli`
- Authentication uses the existing CLI bearer token (same auth flow as normal CLI commands).
- Tenant/project attribution is resolved server-side from token scope.

## Guardrails

- Only internal dogfeeding tenants are accepted server-side.
- Allowlist source:
  - `BILLING_BYPASS_TENANT_IDS`
- If tenant is not allowlisted, telemetry is accepted as no-op (`accepted: false`) and not persisted.

## Error Recovery Order

When dogfeeding exposes broken CLI telemetry, unexpected output, or a missing maintainer capability:

1. Update the relevant current artifact first: `@analyticscli/cli@preview` for CLI behavior, this skill for maintainer instructions, and `@analyticscli/sdk` only when the repro involves SDK instrumentation.
2. Rerun the smallest sanitized repro.
3. If no newer version is available, the update is blocked, or the newest version still fails, submit AnalyticsCLI product feedback with `analyticscli feedback submit`.

Feedback should include the version/update attempt, failing command or endpoint, sanitized payload shape, expected behavior, actual behavior, request id if present, and workaround. This is maintainer feedback about AnalyticsCLI itself, not tenant-owned end-user feedback.

## Data Model

- Telemetry events are stored in ClickHouse `events_raw`.
- Event names emitted by CLI:
  - `cli:command_started`
  - `cli:command_succeeded`
  - `cli:command_failed`
  - `cli:parse_failed`
- `projectSurface` remains `cli` semantics via event naming and props.

## Activation Checklist (Internal)

1. Set `ANALYTICSCLI_SELF_TRACKING_ENABLED=true` in maintainer runtime env.
2. Ensure the maintainer tenant ID is listed in API env `BILLING_BYPASS_TENANT_IDS`.
3. Log in with CLI (`analyticscli setup` or `analyticscli login`) so bearer token is available.
4. Execute CLI commands and verify events via dashboard/queries for the internal project.

## Non-Goals

- Do not require tenants to configure telemetry env variables.
- Do not expose this as public tenant integration guidance.
- Do not require `ANALYTICSCLI_SELF_TRACKING_API_KEY` for the standard internal flow.
