---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:35:49.943Z'
id: useCanGoBack
title: useCanGoBack hook
---

`useCanGoBack` 钩子返回一个布尔值，表示路由历史记录是否可以安全地返回而不退出应用程序。

> ⚠️ 以下新的 `useCanGoBack` API 目前处于 _实验性_ 阶段。

## useCanGoBack 返回值

- 如果路由历史记录索引不为 `0`，则返回 `true`。
- 如果路由历史记录索引为 `0`，则返回 `false`。

## 限制

当使用 [`reloadDocument`](./NavigateOptionsType.md#reloaddocument) 设置为 `true` 进行导航后，路由历史记录索引会被重置。这会导致路由历史记录将新位置视为初始位置，并使 `useCanGoBack` 返回 `false`。

## 示例

### 显示返回按钮

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
