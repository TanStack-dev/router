---
source-updated-at: '2025-02-14T22:19:01.000Z'
translation-updated-at: '2025-05-08T20:14:50.670Z'
id: create-route-property-order
title: 建立路由屬性順序
---

以下函式中，傳入物件的屬性順序會影響型別推論 (type inference)，因此需特別注意：

- `createRoute`
- `createFileRoute`
- `createRootRoute`
- `createRootRouteWithContext`

正確的屬性順序如下：

- `params`、`validateSearch`
- `loaderDeps`、`search.middlewares`
- `context`
- `beforeLoad`
- `loader`
- `onEnter`、`onStay`、`onLeave`、`head`、`scripts`、`headers`、`remountDeps`

其餘屬性因不依賴型別推論 (type inference)，順序不受影響。

## 規則詳情

以下為 **錯誤** 程式碼範例：

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

以下為 **正確** 程式碼範例：

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

## 屬性

- [x] ✅ 推薦
- [x] 🔧 可自動修復
