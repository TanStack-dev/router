---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:43:50.753Z'
id: ToOptionsType
title: ToOptions type
---

`ToOptions` 类型包含多个属性，可用于描述路由目标。

```tsx
type ToOptions = {
  from?: ValidRoutePath | string
  to?: ValidRoutePath | string
  hash?: true | string | ((prev?: string) => string)
  state?: true | HistoryState | ((prev: HistoryState) => HistoryState)
} & SearchParamOptions &
  PathParamOptions

type SearchParamOptions = {
  search?: true | TToSearch | ((prev: TFromSearch) => TToSearch)
}

type PathParamOptions = {
  path?: true | Record<string, TPathParam> | ((prev: TFromParams) => TToParams)
}
```
