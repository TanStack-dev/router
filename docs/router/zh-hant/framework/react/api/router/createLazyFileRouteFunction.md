---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:22.091Z'
id: createLazyFileRouteFunction
title: createLazyFileRoute function
---

`createLazyFileRoute` 函式用於建立一個基於檔案的延遲載入路由實例，該實例僅在匹配時才會載入。此路由實例只能用於配置路由的 [非關鍵屬性](../../guide/code-splitting.md#how-does-tanstack-router-split-code)，例如 `component`、`pendingComponent`、`errorComponent` 和 `notFoundComponent`。

## createLazyFileRoute 選項

`createLazyFileRoute` 函式接受一個類型為 `string` 的參數，代表路由將從中生成的檔案 `path`。

### `path`

- 類型: `string`
- 必填，但 **會由 `tsr generate` 和 `tsr watch` 指令自動插入並更新**
- 路由將從中生成的完整檔案路徑。

### createLazyFileRoute 回傳值

一個新的函式，該函式接受一個部分 [`RouteOptions`](./RouteOptionsType.md) 類型的參數，用於配置檔案 [`Route`](./RouteType.md) 實例。

- 類型:

```tsx
Pick<
  RouteOptions,
  'component' | 'pendingComponent' | 'errorComponent' | 'notFoundComponent'
>
```

- [`RouteOptions`](./RouteOptionsType.md)

> ⚠️ 注意：為了讓 `tsr generate` 和 `tsr watch` 正常運作，檔案路由實例必須使用 `Route` 識別符從檔案中匯出。

### 範例

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/')({
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return <div>{data}</div>
}
```
