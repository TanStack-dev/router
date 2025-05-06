---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:47:56.601Z'
id: RouterEventsType
title: RouterEvents type
---

`RouterEvents` 类型包含了路由器可以触发的所有事件。该类型的每个顶级键名代表路由器可能触发的事件名称，键值则对应事件的有效载荷。

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

## RouterEvents 属性

事件触发时，其有效载荷将包含以下属性。

### `type` 属性

- 类型: `onBeforeNavigate | onBeforeLoad | onLoad | onBeforeRouteMount | onResolved`
- 事件类型
- 该属性可用于在监听函数中区分不同事件。

### `fromLocation` 属性

- 类型: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器当前所处的来源位置。

### `toLocation` 属性

- 类型: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器将要跳转的目标位置。

### `pathChanged` 属性

- 类型: `boolean`
- 若 `fromLocation` 和 `toLocation` 之间的路径发生变化则为 `true`。

### `hrefChanged` 属性

- 类型: `boolean`
- 若 `fromLocation` 和 `toLocation` 之间的完整链接发生变化则为 `true`。

## 示例

```tsx
import { createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

const router = createRouter({ routeTree })

const unsub = router.subscribe('onResolved', (evt) => {
  // ...
})
```
