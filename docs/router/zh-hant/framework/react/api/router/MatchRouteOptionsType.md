---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:14.797Z'
id: MatchRouteOptionsType
title: MatchRouteOptions type
---

`MatchRouteOptions` 型別用於描述匹配路由時可用的選項。

```tsx
interface MatchRouteOptions {
  pending?: boolean
  caseSensitive?: boolean
  includeSearch?: boolean
  fuzzy?: boolean
}
```

## MatchRouteOptions 屬性

`MatchRouteOptions` 型別具有以下屬性：

### `pending` 屬性

- 型別：`boolean`
- 可選
- 若為 `true`，將針對待處理位置 (pending location) 進行匹配，而非當前位置

### `caseSensitive` 屬性

- 型別：`boolean`
- 可選
- 若為 `true`，將以區分大小寫的方式匹配當前位置

### `includeSearch` 屬性

- 型別：`boolean`
- 可選
- 若為 `true`，將使用深度包含檢查來匹配當前位置的搜尋參數 (search params)。例如：`{ a: 1 }` 會匹配當前位置為 `{ a: 1, b: 2 }` 的情況

### `fuzzy` 屬性

- 型別：`boolean`
- 可選
- 若為 `true`，將使用模糊匹配來比對當前位置。例如：`/posts` 會匹配當前位置為 `/posts/123` 的情況
