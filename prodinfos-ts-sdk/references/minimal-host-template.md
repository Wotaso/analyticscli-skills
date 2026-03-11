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
import * as Application from 'expo-application';
import { Platform } from 'react-native';
import { init } from '@prodinfos/sdk-ts';

export const analytics = init({
  apiKey: process.env.EXPO_PUBLIC_PRODINFOS_WRITE_KEY,
  debug: typeof __DEV__ === 'boolean' ? __DEV__ : false,
  platform:
    Platform.OS === 'ios' ||
    Platform.OS === 'android' ||
    Platform.OS === 'windows' ||
    Platform.OS === 'macos'
      ? Platform.OS === 'macos'
        ? 'mac'
        : Platform.OS
      : undefined,
  appVersion: Application.nativeApplicationVersion ?? undefined,
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

- `mapEventToCanonical(...)` with many branches
- giant generic `trackEvent(...)` indirection for all product events
- per-call `try/catch` wrappers around every SDK call
- `Promise<AnalyticsClient | null>` bootstrap patterns
- `platform: 'react-native'` (use canonical `ios`/`android`/`mac`/`windows`/`web` or omit)
- explicit `endpoint` in host app code
