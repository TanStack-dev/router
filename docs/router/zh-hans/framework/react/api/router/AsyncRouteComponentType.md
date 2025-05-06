---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:55:27.192Z'
id: AsyncRouteComponentType
title: AsyncRouteComponent type
---

`AsyncRouteComponent` 类型用于描述一个支持代码分割的路由组件，该组件可通过 `component.preload()` 方法进行预加载。

```tsx
type AsyncRouteComponent<TProps> = SyncRouteComponent<TProps> & {
  preload?: () => Promise<void>
}
```
