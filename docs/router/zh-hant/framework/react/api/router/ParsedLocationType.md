---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:53.344Z'
id: ParsedLocationType
title: ParsedLocation type
---

`ParsedLocation` 類型代表 TanStack Router 中已解析的位置。它包含許多關於當前位置的有用資訊，包括路徑名稱 (pathname)、搜尋參數 (search params)、雜湊值 (hash)、位置狀態 (location state) 以及路由遮罩資訊 (route masking information)。

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
