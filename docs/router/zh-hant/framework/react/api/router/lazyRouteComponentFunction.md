---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:00.383Z'
id: lazyRouteComponentFunction
title: lazyRouteComponent function
---

> [!IMPORTANT]
> 如果您使用基於檔案的路由 (file-based routing)，建議改用 `createLazyFileRoute` 函式。

`lazyRouteComponent` 函式可用來建立一次性程式碼分割 (code-split) 的路由元件 (route component)，並可透過 `component.preload()` 方法進行預載 (preload)。

## lazyRouteComponent 選項

`lazyRouteComponent` 函式接受兩個參數：

### `importer` 選項

- 類型：`() => Promise<T>`
- 必填
- 一個回傳 Promise 的函式，該 Promise 會解析為包含待載入元件的物件。

### `exportName` 選項

- 類型：`string`
- 選填
- 從匯入物件中載入的元件名稱，預設為 `'default'`。

## lazyRouteComponent 回傳值

- 一個 `React.lazy` 元件，可透過 `component.preload()` 方法進行預載。

## 範例

```tsx
import { lazyRouteComponent } from '@tanstack/react-router'

const route = createRoute({
  path: '/posts/$postId',
  component: lazyRouteComponent(() => import('./Post')), // 預設匯出 (default export)
})

// 或

const route = createRoute({
  path: '/posts/$postId',
  component: lazyRouteComponent(
    () => import('./Post'),
    'PostByIdPageComponent', // 具名匯出 (named export)
  ),
})
```
