---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:45:42.333Z'
id: RouterStateType
title: RouterState type
---

`RouterState` 类型表示路由器的内部状态结构。当需要访问路由器的某些内部信息时（例如待处理的匹配项、路由器是否处于加载状态等），路由器的内部状态非常有用。

```tsx
type RouterState = {
  status: 'pending' | 'idle'
  isLoading: boolean
  isTransitioning: boolean
  matches: Array<RouteMatch>
  pendingMatches: Array<RouteMatch>
  location: ParsedLocation
  resolvedLocation: ParsedLocation
}
```

## RouterState 属性

`RouterState` 类型包含路由器状态中所有可用的属性。

### `status` 属性

- 类型: `'pending' | 'idle'`
- 路由器的当前状态。如果状态为 `pending`，表示路由器正在加载路由或仍在过渡到新路由。

### `isLoading` 属性

- 类型: `boolean`
- 如果路由器正在加载路由或等待路由完成加载，则为 `true`。

### `isTransitioning` 属性

- 类型: `boolean`
- 如果路由器当前正在过渡到新路由，则为 `true`。

### `matches` 属性

- 类型: [`Array<RouteMatch>`](./RouteMatchType.md)
- 所有已解析且当前处于活动状态的路由匹配项的数组。

### `pendingMatches` 属性

- 类型: [`Array<RouteMatch>`](./RouteMatchType.md)
- 所有当前待处理的路由匹配项的数组。

### `location` 属性

- 类型: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器从浏览器历史记录中解析的最新位置。此位置可能尚未被解析或加载。

### `resolvedLocation` 属性

- 类型: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器已解析并加载的位置。
