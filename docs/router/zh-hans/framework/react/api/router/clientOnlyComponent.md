---
source-updated-at: '2025-04-13T08:50:44.000Z'
translation-updated-at: '2025-05-06T22:01:48.536Z'
id: clientOnlyComponent
title: ClientOnly Component
---

`ClientOnly` 组件用于仅在客户端渲染组件，避免因水合错误导致服务端渲染 (SSR) 中断。它接受一个 `fallback` 属性，当客户端尚未加载 JS 时会渲染该回退内容。

## 属性

`ClientOnly` 组件接受以下属性：

### `props.fallback` 属性

当客户端尚未加载 JS 时需要渲染的回退组件。

### `props.children` 属性

当客户端已加载 JS 时需要渲染的组件。

## 返回值

- 若客户端已加载 JS，则返回组件的子元素。
- 若客户端尚未加载 JS，则返回 `fallback` 组件。

## 示例

```tsx
// src/routes/dashboard.tsx
import { ClientOnly, createFileRoute } from '@tanstack/react-router'
import {
  Charts,
  FallbackCharts,
} from './charts-that-break-server-side-rendering'

export const Route = createFileRoute('/dashboard')({
  component: Dashboard,
  // ... 其他路由选项
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
