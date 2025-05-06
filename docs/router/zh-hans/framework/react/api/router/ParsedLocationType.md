---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:53:08.179Z'
id: ParsedLocationType
title: ParsedLocation type
---

`ParsedLocation` 类型表示 TanStack Router 中已解析的路由位置信息。它包含当前路由位置的大量实用信息，包括路径名 (pathname)、搜索参数 (search params)、哈希值 (hash)、位置状态 (location state) 以及路由掩码信息 (route masking information)。

```tsx
interface ParsedLocation {
  href: string
  pathname: string
  search: TFullSearchSchema
  searchStr: string
  state: ParsedHistoryState
  hash: string
  maskedLocation?: ParsedLocation
  unmaskOnReload?: boolean
}
```
