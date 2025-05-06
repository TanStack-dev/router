---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:37:01.000Z
title: 未找到错误
---

> ⚠️ 本文档介绍用于处理404错误的新版`notFound`函数和`notFoundComponent` API。`NotFoundRoute`路由已弃用，将在未来版本中移除。更多信息请参阅[从`NotFoundRoute`迁移](#migrating-from-notfoundroute)。

## 概述

TanStack Router中404错误有两种使用场景：

- **未匹配的路由路径**：当路径不匹配任何已知路由模式**或**部分匹配路由但包含额外路径段时
  - **路由器**会在路径不匹配任何已知路由模式时自动抛出404错误
  - 若路由器`notFoundMode`设为`fuzzy`，将使用最近父路由的`notFoundComponent`处理错误；若设为`root`，则由根路由处理错误
  - 示例：
    - 访问不存在的`/users`路由
    - 当路由树仅处理`/posts/$postId`时访问`/posts/1/edit`
- **缺失的资源**：当资源不存在时，如指定ID的文章或任何不可用/不存在的异步数据
  - **开发者**需在资源不存在时手动抛出404错误，可通过`beforeLoad`或`loader`函数使用`notFound`工具实现
  - 由最近父路由的`notFoundComponent`或根路由处理
  - 示例：
    - 访问不存在的ID为1的文章`/posts/1`
    - 访问不存在的文档`/docs/path/to/document`

这两种情况底层都使用相同的`notFound`函数和`notFoundComponent` API实现。

## `notFoundMode`选项

当TanStack Router遇到**不匹配任何已知路由模式**或**部分匹配但包含额外路径段**的路径时，将自动抛出404错误。

根据`notFoundMode`选项，路由器会以不同方式处理这些自动错误：

- ["fuzzy"模式](#notfoundmode-fuzzy)（默认）：智能寻找最近匹配的合适路由并显示其`notFoundComponent`
- ["root"模式](#notfoundmode-root)：所有404错误都由根路由的`notFoundComponent`处理

### `notFoundMode: 'fuzzy'`

默认情况下，路由器的`notFoundMode`设为`fuzzy`，表示当路径不匹配任何已知路由时，路由器将尝试使用最近匹配的、具有子路由（即有Outlet）且配置了404组件的路由。

> **❓ 为何这是默认设置？** 模糊匹配能保留尽可能多的父级布局，为用户提供更多上下文信息以便基于预期位置导航到有效路径。

最近合适路由的选择标准：

- 必须有子路由（因此有`Outlet`可渲染`notFoundComponent`）
- 必须配置了`notFoundComponent`或路由器配置了`defaultNotFoundComponent`

### `notFoundMode: 'root'`

当`notFoundMode`设为`root`时，所有404错误都将由根路由的`notFoundComponent`处理，而非从最近模糊匹配的路由冒泡上来。

## 配置路由的`notFoundComponent`

通过给路由附加`notFoundComponent`可处理两种类型的404错误。例如：

```tsx
export const Route = createFileRoute('/settings')({
  component: () => (
    <div>
      <p>设置页面</p>
      <Outlet />
    </div>
  ),
  notFoundComponent: () => <p>该设置页面不存在！</p>,
})
```

或处理不存在的文章：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post) throw notFound()
    return { post }
  },
  component: ({ post }) => (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  ),
  notFoundComponent: () => <p>文章未找到！</p>,
})
```

## 默认全局404处理

可通过`createRouter`配置全应用范围的默认404组件（仅对有子路由的路径有效）：

```tsx
const router = createRouter({
  defaultNotFoundComponent: () => (
    <div>
      <p>未找到页面！</p>
      <Link to="/">返回首页</Link>
    </div>
  ),
})
```

## 手动抛出`notFound`错误

在loader方法或组件中使用`notFound`函数手动抛出404错误：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params: { postId } }) => {
    const post = await getPost(postId)
    if (!post) throw notFound() // 或 notFound({ throw: true })
    return { post }
  },
})
```

## 指定处理404错误的路由

通过`notFound`函数的`route`选项可指定特定父路由处理404错误：

```tsx
throw notFound({ routeId: '/_pathlessLayout' })
```

### 手动指定根路由

```tsx
import { rootRouteId } from '@tanstack/react-router'
throw notFound({ routeId: rootRouteId })
```

### 在组件中抛出404错误

虽然支持，但**推荐在loader方法中抛出以避免闪烁问题**。可使用`CatchNotFound`组件捕获组件中的404错误。

## `notFoundComponent`中的数据加载

在`notFoundComponent`中：

- **`SomeRoute.useLoaderData`可能未定义**
- 但`Route.useParams`、`Route.useSearch`等方法会返回有效值
- 可通过`notFound`函数的`data`选项传递不完整的loader数据

## SSR使用

详见[SSR指南](./ssr.md)。

## 从`NotFoundRoute`迁移

主要变更：

- 移除`notFoundRoute`导入
- 在根路由添加`notFoundComponent`
- 注意`notFoundComponent`不支持渲染`<Outlet>`

```tsx
export const Route = createRootRoute({
  notFoundComponent: () => <p>未找到页面！</p>,
})
```
