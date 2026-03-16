# Storage Options

Storage is optional in `@analyticscli/sdk`.

- Without storage: fastest setup, but IDs reset after app restarts.
- With storage: stable `anonId` and `sessionId` across restarts, better continuity for retention and funnels.

## Adapter Options

| Strategy | Good for | Tradeoff |
| --- | --- | --- |
| No adapter | Fast prototypes and short sessions | IDs reset across restarts |
| `@react-native-async-storage/async-storage` | Standard React Native persistence | Async hydration happens in background; call `ready()` only when you must block first event |
| `react-native-mmkv` | Fast local key-value storage in RN | Native dependency |
| Custom adapter | Existing secure or encrypted store | You own the wrapper |

## Minimal Example

```ts
import { init } from '@analyticscli/sdk';

const analytics = init('<YOUR_APP_KEY>');
```

## AsyncStorage Example

```ts
import AsyncStorage from '@react-native-async-storage/async-storage';
import * as Application from 'expo-application';
import { Platform } from 'react-native';
import { init } from '@analyticscli/sdk';

const analytics = init({
  apiKey:
    process.env.EXPO_PUBLIC_ANALYTICSCLI_PUBLISHABLE_API_KEY ??
    process.env.EXPO_PUBLIC_ANALYTICSCLI_WRITE_KEY,
  debug: __DEV__,
  platform: Platform.OS,
  appVersion: Application.nativeApplicationVersion,
  storage: AsyncStorage,
});
```

## MMKV Example

```ts
import { MMKV } from 'react-native-mmkv';
import { Platform } from 'react-native';
import { init } from '@analyticscli/sdk';

const kv = new MMKV();

const analytics = init({
  apiKey:
    process.env.EXPO_PUBLIC_ANALYTICSCLI_PUBLISHABLE_API_KEY ??
    process.env.EXPO_PUBLIC_ANALYTICSCLI_WRITE_KEY,
  debug: __DEV__,
  platform: Platform.OS,
  storage: {
    getItem: (key) => kv.getString(key) ?? null,
    setItem: (key, value) => kv.set(key, value),
    removeItem: (key) => kv.delete(key),
  },
});
```
