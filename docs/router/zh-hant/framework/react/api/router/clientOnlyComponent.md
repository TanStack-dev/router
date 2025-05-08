---
source-updated-at: '2025-04-13T08:50:44.000Z'
translation-updated-at: '2025-05-08T20:22:19.411Z'
id: clientOnlyComponent
title: ClientOnly Component
---

`ClientOnly` 元件用於僅在客戶端渲染元件，避免因水合 (hydration) 錯誤而破壞伺服器渲染 (SSR)。它接受一個 `fallback` 屬性，當客戶端尚未載入 JS 時會渲染此備用內容。

## 屬性

`ClientOnly` 元件接受以下屬性：

### `props.fallback` 屬性

當客戶端尚未載入 JS 時，將渲染的備用元件。

### `props.children` 屬性

當客戶端已載入 JS 時，將渲染的子元件。

## 回傳值

- 若客戶端已載入 JS，則回傳元件的子元件。
- 若客戶端尚未載入 JS，則回傳 `fallback` 備用元件。

## 範例

```tsx
// src/routes/dashboard.tsx
import { ClientOnly, createFileRoute } from '@tanstack/react-router'
import {
  Charts,
  FallbackCharts,
} from './charts-that-break-server-side-rendering'

export const Route = createFileRoute('/dashboard')({
  component: Dashboard,
  // ... 其他路由選項
})

function Dashboard() {
  return (
    <div>
      <p>Dashboard</p>
      <ClientOnly fallback={<FallbackCharts />}>
        <Charts />
      </ClientOnly>
    </div>
  )
}
```
