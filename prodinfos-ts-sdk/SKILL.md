---
name: prodinfos-ts-sdk
description: Use when integrating or upgrading the Prodinfos TypeScript SDK in web, TypeScript, React Native, or Expo apps.
license: MIT
homepage: https://github.com/wotaso/prodinfos-skills
metadata: {"author":"wotaso","version":"1.0.0","prodinfos-target":"@prodinfos/sdk-ts","prodinfos-supported-range":">=0.1.0-preview.0 <0.2.0","openclaw":{"emoji":"🧩","homepage":"https://github.com/wotaso/prodinfos-skills"}}
---

# Prodinfos TypeScript SDK

## Use This Skill When

- adding Prodinfos analytics to a JS or TS app
- instrumenting onboarding, paywall, purchase, or survey events
- upgrading within the current `@prodinfos/sdk-ts` line
- validating SDK behavior together with `@prodinfos/cli`

## Supported Versions

- Skill pack: `1.0.0`
- Target package: `@prodinfos/sdk-ts`
- Supported range: `>=0.1.0-preview.0 <0.2.0`
- If a future SDK major changes APIs or event contracts in incompatible ways, add a sibling skill such as `prodinfos-ts-sdk-v1`

See [Versioning Notes](references/versioning.md).

## Core Rules

- Initialize exactly once near app bootstrap.
- `runtimeEnv` is auto-attached. Do not pass a `mode` string.
- `debug` is only a boolean for SDK console logging.
- Prefer SDK constants and tracker helpers over ad-hoc event names.
- Keep event properties stable and query-relevant.
- Avoid direct PII.
- Use storage when you need stable `anonId` and `sessionId` across restarts.

## Minimal Web Setup

```ts
import { init } from '@prodinfos/sdk-ts';

const analytics = init({
  apiKey: process.env.PRODINFOS_WRITE_KEY!,
  projectId: process.env.PRODINFOS_PROJECT_ID!,
  endpoint: 'https://collector.prodinfos.com',
  debug: false,
  platform: 'web',
  appVersion: '1.0.0',
  dedupeOnboardingStepViewsPerSession: true,
});
```

## React Native Setup

```ts
import AsyncStorage from '@react-native-async-storage/async-storage';
import { initAsync } from '@prodinfos/sdk-ts';

const analytics = await initAsync({
  apiKey: process.env.PRODINFOS_WRITE_KEY!,
  projectId: process.env.PRODINFOS_PROJECT_ID!,
  endpoint: 'https://collector.prodinfos.com',
  debug: typeof __DEV__ === 'boolean' ? __DEV__ : false,
  platform: 'react-native',
  appVersion: '1.0.0',
  dedupeOnboardingStepViewsPerSession: true,
  storage: {
    getItem: (key) => AsyncStorage.getItem(key),
    setItem: (key, value) => AsyncStorage.setItem(key, value),
    removeItem: (key) => AsyncStorage.removeItem(key),
  },
});
```

## Instrumentation Rules

- Use `createOnboardingTracker(...)` for onboarding flows.
- Use `trackPaywallEvent(...)` for paywall and purchase milestones.
- Use canonical event names from `ONBOARDING_EVENTS`, `PAYWALL_EVENTS`, and `PURCHASE_EVENTS`.
- Keep `onboardingFlowId`, `onboardingFlowVersion`, `paywallId`, `source`, and `appVersion` stable.

## Validation Loop

After integration or upgrade, verify ingestion with stable CLI checks:

```bash
prodinfos schema events --project <projectId>
prodinfos goal-completion --project <projectId> --start onboarding:start --complete onboarding:complete --last 30d
prodinfos get onboarding-journey --project <projectId> --last 30d --format text
```

## References

- [Onboarding And Paywall Contract](references/onboarding-paywall.md)
- [Storage Options](references/storage.md)
- [Versioning Notes](references/versioning.md)
