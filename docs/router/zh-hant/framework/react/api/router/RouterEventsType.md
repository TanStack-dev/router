---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:50.469Z'
id: RouterEventsType
title: RouterEvents type
---

`RouterEvents` 類型包含路由器可以發出的所有事件。此類型的每個頂層鍵代表路由器可以發出的事件名稱，鍵值則是該事件的承載資料。

```tsx
type RouterEvents = {
  onBeforeNavigate: {
    type: 'onBeforeNavigate'
    fromLocation?: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onBeforeLoad: {
    type: 'onBeforeLoad'
    fromLocation?: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onLoad: {
    type: 'onLoad'
    fromLocation?: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onResolved: {
    type: 'onResolved'
    fromLocation?: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onBeforeRouteMount: {
    type: 'onBeforeRouteMount'
    fromLocation?: ParsedLocation
    toLocation: ParsedLocation
    pathChanged: boolean
    hrefChanged: boolean
  }
  onInjectedHtml: {
    type: 'onInjectedHtml'
    promise: Promise<string>
  }
  onRendered: {
    type: 'onRendered'
    fromLocation?: ParsedLocation
    toLocation: ParsedLocation
  }
}
```

## RouterEvents 屬性

當事件被發出時，事件承載資料將包含以下屬性。

### `type` 屬性

- 類型: `onBeforeNavigate | onBeforeLoad | onLoad | onBeforeRouteMount | onResolved`
- 事件類型
- 此屬性可用於在監聽函式中區分不同事件。

### `fromLocation` 屬性

- 類型: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器正在離開的導航位置。

### `toLocation` 屬性

- 類型: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器即將導航至的位置。

### `pathChanged` 屬性

- 類型: `boolean`
- 若 `fromLocation` 和 `toLocation` 之間的路徑發生變化，則為 `true`。

### `hrefChanged` 屬性

- 類型: `boolean`
- 若 `fromLocation` 和 `toLocation` 之間的網址發生變化，則為 `true`。

## 範例

```tsx
import { createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = createRouter({ routeTree })

const unsub = router.subscribe('onResolved', (evt) => {
  // ...
})
```
