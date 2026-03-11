# Minimal Host Template

Use this as the default shape for host-app integration.

## Goal

Keep host code small and explicit:

- one bootstrap location
- direct SDK calls in feature code
- no large translation layer

## Bootstrap Template (React Native / Expo)

```ts
import AsyncStorage from '@react-native-async-storage/async-storage';
import { initFromEnv } from '@prodinfos/sdk-ts';

export const analytics = initFromEnv({
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

void analytics.ready();
```

## Call-Site Template

```ts
import { PAYWALL_EVENTS, PURCHASE_EVENTS } from '@prodinfos/sdk-ts';
import { analytics } from '@/utils/analytics';

analytics.screen('onboarding_region');

analytics.trackPaywallEvent(PAYWALL_EVENTS.SHOWN, {
  source: 'onboarding',
  paywallId: 'default_paywall',
  fromScreen: 'onboarding_offer',
});

analytics.trackPaywallEvent(PURCHASE_EVENTS.SUCCESS, {
  source: 'onboarding',
  paywallId: 'default_paywall',
  packageId: 'annual',
});
```

## Anti-Patterns

Do not generate by default:

- `mapLegacyEventToCanonical(...)` with many branches
- giant generic `trackEvent(...)` indirection for all product events
- per-call `try/catch` wrappers around every SDK call
- `Promise<AnalyticsClient | null>` bootstrap patterns

## Compatibility Escape Hatch

If the user explicitly requires a temporary compatibility phase:

- isolate aliases in one small map
- keep the shim temporary and documented
- schedule cleanup to direct canonical calls
