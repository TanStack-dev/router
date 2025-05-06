---
source-updated-at: 2025-02-14T22:19:01.000Z
translation-updated-at: 2025-04-05T03:38:32.000Z
title: 创建路由属性顺序
id: create-route-property-order
---

对于以下函数，由于类型推断的原因，传入对象的属性顺序非常重要：

- `createRoute`
- `createFileRoute`
- `createRootRoute`
- `createRootRouteWithContext`

正确的属性顺序如下：

- `params`, `validateSearch`
- `loaderDeps`, `search.middlewares`
- `context`
- `beforeLoad`
- `loader`
- `onEnter`, `onStay`, `onLeave`, `head`, `scripts`, `headers`, `remountDeps`

其他所有属性对顺序不敏感，因为它们不依赖于类型推断。

## 规则详情

该规则的**错误**代码示例：

```tsx
/* eslint "@tanstack/router/create-route-property-order": "warn" */
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/foo/bar/$id')({
  loader: async ({context}) => {
    await context.queryClient.ensureQueryData(getQueryOptions(context.hello)),
  },
  beforeLoad: () => ({hello: 'world'})
})
```

该规则的**正确**代码示例：

```tsx
/* eslint "@tanstack/router/create-route-property-order": "warn" */
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/foo/bar/$id')({
  beforeLoad: () => ({hello: 'world'}),
  loader: async ({context}) => {
    await context.queryClient.ensureQueryData(getQueryOptions(context.hello)),
  }
})
```

## 特性

- [x] ✅ 推荐
- [x] 🔧 可自动修复
