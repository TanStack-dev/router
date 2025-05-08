---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:25.316Z'
id: useCanGoBack
title: useCanGoBack hook
---

`useCanGoBack` 鉤子 (hook) 會回傳一個布林值 (boolean)，表示路由器的歷史記錄是否可以安全地返回而不會退出應用程式。

> ⚠️ 以下新的 `useCanGoBack` API 目前處於 _實驗階段_。

## useCanGoBack 回傳值

- 如果路由器歷史記錄的索引 (index) 不在 `0`，回傳 `true`。
- 如果路由器歷史記錄的索引 (index) 為 `0`，回傳 `false`。

## 限制

當導航時設定了 [`reloadDocument`](./NavigateOptionsType.md#reloaddocument) 為 `true`，路由器歷史記錄的索引會被重置。這會導致路由器歷史記錄將新位置視為初始位置，並使 `useCanGoBack` 回傳 `false`。

## 範例

### 顯示返回按鈕

```tsx
import { useRouter, useCanGoBack } from '@tanstack/react-router'

function Component() {
  const router = useRouter()
  const canGoBack = useCanGoBack()

  return (
    <div>
      {canGoBack ? (
        <button onClick={() => router.history.back()}>Go back</button>
      ) : null}

      {/* ... */}
    </div>
  )
}
```
