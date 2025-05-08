---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:11.638Z'
id: useLocationHook
title: useLocation hook
---

`useLocation` 方法是一個鉤子 (hook)，它會回傳當前的 [`location`](./ParsedLocationType.md) 物件。當你想要在當前位置變更時執行某些副作用 (side effect) 時，這個鉤子會非常有用。

## useLocation 選項

`useLocation` 鉤子接受一個可選的 `options` 物件。

### `opts.select` 選項

- 類型: `(state: ParsedLocationType) => TSelected`
- 可選
- 如果提供此函式，它會以 [`location`](./ParsedLocationType.md) 物件作為參數被呼叫，並且其回傳值會從 `useLocation` 回傳。

## useLocation 回傳值

- 當前的 [`location`](./ParsedLocationType.md) 物件，如果提供了 `select` 函式則回傳 `TSelected`。

## 範例

```tsx
import { useLocation } from '@tanstack/react-router'

function Component() {
  const location = useLocation()
  //    ^ ParsedLocation

  // 或者

  const pathname = useLocation({
    select: (location) => location.pathname,
  })
  //    ^ string

  // ...
}
```
