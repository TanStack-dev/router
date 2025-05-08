---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:20.021Z'
id: createFileRouteFunction
title: createFileRoute function
---

`createFileRoute` 函式是一個工廠方法，可用於建立基於檔案的 路由 (route) 實例。此路由實例隨後可用於透過 `tsr generate` 和 `tsr watch` 指令自動產生路由樹 (route tree)。

## createFileRoute 選項

`createFileRoute` 函式接受一個型別為 `string` 的參數，代表將產生路由的檔案路徑 (`path`)。

### `path` 選項

- 型別：`string` 字面值
- 必填，但**會由 `tsr generate` 和 `tsr watch` 指令自動插入並更新**
- 將產生路由的檔案完整路徑

## createFileRoute 回傳值

回傳一個新函式，該函式接受一個型別為 [`RouteOptions`](./RouteOptionsType.md) 的參數，用於配置檔案 [`Route`](./RouteType.md) 實例。

> ⚠️ 注意：為了讓 `tsr generate` 和 `tsr watch` 正常運作，必須使用 `Route` 識別符將檔案路由實例從檔案中匯出。

## 範例

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  loader: () => {
    return 'Hello World'
  },
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return <div>{data}</div>
}
```
