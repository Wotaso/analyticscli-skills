# Storage Options

Storage is optional in `@prodinfos/sdk-ts`.

- Without storage: fastest setup, but IDs reset after app restarts.
- With storage: stable `anonId` and `sessionId` across restarts, better continuity for retention and funnels.

## Adapter Options

| Strategy | Good for | Tradeoff |
| --- | --- | --- |
| No adapter | Fast prototypes and short sessions | IDs reset across restarts |
| `@react-native-async-storage/async-storage` | Standard React Native persistence | Async hydration, so prefer `initAsync(...)` |
| `react-native-mmkv` | Fast local key-value storage in RN | Native dependency |
| Custom adapter | Existing secure or encrypted store | You own the wrapper |

## Minimal Example

```ts
import { init } from '@prodinfos/sdk-ts';

const analytics = init({
  apiKey,
  projectId,
  endpoint,
  debug: false,
});
```

## AsyncStorage Example

```ts
import AsyncStorage from '@react-native-async-storage/async-storage';
import { initAsync } from '@prodinfos/sdk-ts';

const analytics = await initAsync({
  apiKey,
  projectId,
  endpoint,
  debug: typeof __DEV__ === 'boolean' ? __DEV__ : false,
  platform: 'react-native',
  storage: {
    getItem: (key) => AsyncStorage.getItem(key),
    setItem: (key, value) => AsyncStorage.setItem(key, value),
    removeItem: (key) => AsyncStorage.removeItem(key),
  },
});
```

## MMKV Example

```ts
import { MMKV } from 'react-native-mmkv';
import { init } from '@prodinfos/sdk-ts';

const kv = new MMKV();

const analytics = init({
  apiKey,
  projectId,
  endpoint,
  debug: typeof __DEV__ === 'boolean' ? __DEV__ : false,
  storage: {
    getItem: (key) => kv.getString(key) ?? null,
    setItem: (key, value) => kv.set(key, value),
    removeItem: (key) => kv.delete(key),
  },
});
```
