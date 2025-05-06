---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:54:29.734Z'
id: MatchRouteOptionsType
title: MatchRouteOptions type
---

`MatchRouteOptions` 类型用于描述匹配路由时可用的选项。

```tsx
interface MatchRouteOptions {
  pending?: boolean
  caseSensitive?: boolean
  includeSearch?: boolean
  fuzzy?: boolean
}
```

## MatchRouteOptions 属性

`MatchRouteOptions` 类型包含以下属性：

### `pending` 属性

- 类型: `boolean`
- 可选
- 如果为 `true`，将针对待定位置 (pending location) 而非当前位置进行匹配

### `caseSensitive` 属性

- 类型: `boolean`
- 可选
- 如果为 `true`，将启用大小写敏感模式来匹配当前位置

### `includeSearch` 属性

- 类型: `boolean`
- 可选
- 如果为 `true`，将使用深度包含检查来匹配当前位置的搜索参数 (search params)。例如：`{ a: 1 }` 可以匹配当前位置 `{ a: 1, b: 2 }`

### `fuzzy` 属性

- 类型: `boolean`
- 可选
- 如果为 `true`，将使用模糊匹配模式来匹配当前位置。例如：`/posts` 可以匹配当前位置 `/posts/123`
