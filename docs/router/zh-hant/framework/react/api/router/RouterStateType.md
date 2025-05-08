---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:41.047Z'
id: RouterStateType
title: RouterState type
---

`RouterState` 型別代表路由器的內部狀態結構。當你需要存取路由器的某些內部資訊時（例如任何待處理的匹配、路由器是否處於載入狀態等），路由器的內部狀態會非常有用。

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

## RouterState 屬性

`RouterState` 型別包含路由器狀態中所有可用的屬性。

### `status` 屬性

- 型別: `'pending' | 'idle'`
- 路由器的當前狀態。如果路由器處於 pending 狀態，表示它正在載入路由或仍在過渡到新路由。

### `isLoading` 屬性

- 型別: `boolean`
- 如果路由器正在載入路由或等待路由完成載入，則為 `true`。

### `isTransitioning` 屬性

- 型別: `boolean`
- 如果路由器正在過渡到新路由，則為 `true`。

### `matches` 屬性

- 型別: [`Array<RouteMatch>`](./RouteMatchType.md)
- 所有已解析且當前處於活動狀態的路由匹配陣列。

### `pendingMatches` 屬性

- 型別: [`Array<RouteMatch>`](./RouteMatchType.md)
- 所有當前待處理的路由匹配陣列。

### `location` 屬性

- 型別: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器從瀏覽器歷史記錄中解析的最新位置。此位置可能尚未解析和載入。

### `resolvedLocation` 屬性

- 型別: [`ParsedLocation`](./ParsedLocationType.md)
- 路由器已解析並載入的位置。
