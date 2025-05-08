---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:35.992Z'
id: FileRouteClass
title: FileRoute class
---

> [!CAUTION]
> 此類別已被棄用，並將在 TanStack Router 的下一個主要版本中移除。
> 請改用 [`createFileRoute`](./createFileRouteFunction.md) 函式。

`FileRoute` 類別是一個工廠，可用於建立基於檔案的路由實例。此路由實例隨後可用於透過 `tsr generate` 和 `tsr watch` 指令自動產生路由樹。

## `FileRoute` 建構函式

`FileRoute` 建構函式接受單一參數：路由將為其產生的檔案 `path`。

### 建構函式選項

- 類型：`string` 字面值
- 必填，但 **會由 `tsr generate` 和 `tsr watch` 指令自動插入並更新**。
- 路由將從中產生的檔案完整路徑。

### 建構函式回傳值

- 一個 `FileRoute` 類別的實例，可用於建立路由。

## `FileRoute` 方法

`FileRoute` 類別實作了以下方法：

### `.createRoute` 方法

`createRoute` 方法可用於設定檔案路由實例。它接受單一參數：將用於設定檔案路由實例的 `options`。

#### .createRoute 選項

- 類型：`Omit<RouteOptions, 'getParentRoute' | 'path' | 'id'>`
- [`RouteOptions`](./RouteOptionsType.md)
- 可選
- 與 `Route` 類別相同的選項，但省略了 `getParentRoute`、`path` 和 `id` 選項，因為它們對於基於檔案的路由是不必要的。

#### .createRoute 回傳值

一個 [`Route`](./RouteType.md) 實例，可用於設定要插入路由樹中的路由。

> ⚠️ 注意：為了讓 `tsr generate` 和 `tsr watch` 正常運作，檔案路由實例必須使用 `Route` 識別符從檔案中匯出。

### 範例

```tsx
import { FileRoute } from '@tanstack/react-router'

export const Route = new FileRoute('/').createRoute({
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
