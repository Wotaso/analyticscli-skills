# Minimal Host Template

Use this as the default shape for host-app integration.

## Goal

Keep host code small and explicit:

- one bootstrap location
- direct SDK calls in feature code
- no large translation layer

## Dashboard Credentials

Before bootstrap code is added:

- Open [dash.analyticscli.com](https://dash.analyticscli.com) and select the target project.
- In **API Keys**, copy the publishable ingest API key for SDK init.
- If CLI validation is in scope, create/copy a CLI `readonly_token` in the same **API Keys** area.
- Optional for CLI verification: set a default project once with `analyticscli projects select` (arrow-key picker), or pass `--project <project_id>` per command.

## Bootstrap Template (Web)

```ts
import { init } from '@analyticscli/sdk';

export const analytics = init({
  apiKey: process.env.NEXT_PUBLIC_ANALYTICSCLI_PUBLISHABLE_API_KEY ?? '',
  platform: 'web',
  identityTrackingMode: 'consent_gated', // default
});
```

## Bootstrap Template (React Native / Expo)

```ts
import AsyncStorage from '@react-native-async-storage/async-storage';
import * as Application from 'expo-application';
import { Platform } from 'react-native';
import { init } from '@analyticscli/sdk';

export const analytics = init({
  apiKey: process.env.EXPO_PUBLIC_ANALYTICSCLI_PUBLISHABLE_API_KEY,
  debug: __DEV__,
  platform: Platform.OS,
  appVersion: Application.nativeApplicationVersion,
  identityTrackingMode: 'consent_gated', // default
  storage: AsyncStorage, // optional for persistent IDs after consent
});
```

`ready()` does not start tracking. It is only for blocking flow transitions until async storage hydration finishes.

## Full-Tracking Consent

```ts
// user accepts full tracking
analytics.setFullTrackingConsent(true);

// user declines full tracking but strict analytics can continue
analytics.setFullTrackingConsent(false);
```

## Call-Site Template

```ts
import { analytics } from '@/utils/analytics';

const paywall = analytics.createPaywallTracker({
  source: 'onboarding',
  paywallId: 'default_paywall',
  offering: 'rc_main',
  appVersion: '1.0.0',
});

analytics.screen('onboarding_region');

paywall.shown({
  fromScreen: 'onboarding_offer',
});

paywall.purchaseSuccess({
  packageId: 'annual',
});
```

Create one paywall tracker per stable paywall flow context. Do not recreate a new
`createPaywallTracker(...)` instance for every callback/event.
If your provider exposes it, always pass an `offering` identifier in tracker defaults
(RevenueCat offering, Adapty paywall/placement, Superwall placement/paywall id).

## Anti-Patterns

Do not generate by default:

- `mapEventToCanonical(...)` with many branches
- giant generic `trackEvent(...)` indirection for all product events
- per-call `try/catch` wrappers around every SDK call
- `Promise<AnalyticsClient | null>` bootstrap patterns
- `platform: 'react-native'` (use canonical `ios`/`android`/`mac`/`windows`/`web` or omit)
- explicit `endpoint` in host app code
- creating `createPaywallTracker(...)` inside every paywall callback/event helper
- `apiKey` fallback chains using `*WRITE_KEY*` env vars in host-app code
- duplicate screen tracking from both parent layout and child screen for the same route change
