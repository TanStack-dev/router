---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:20:02.000Z
title: 基于代码的路由
---

> [!TIP]
> 对于大多数应用程序，不建议使用基于代码的路由。推荐使用[基于文件的路由](./file-based-routing.md)替代方案。

## ⚠️ 开始前的注意事项

- 如果正在使用[基于文件的路由](./file-based-routing.md)，**请跳过本指南**。
- 若仍坚持使用基于代码的路由，必须首先阅读[路由核心概念](./routing-concepts.md)指南，该文档也涵盖了路由器的核心概念。

## 路由树

基于代码的路由与基于文件的路由在本质上并无差异，二者均采用相同的路由树概念来组织、匹配路由，并将匹配的路由组合成组件树。唯一区别在于：前者通过代码而非文件系统来管理路由结构。

以下是将[路由树与嵌套](./route-trees.md#route-trees)指南中的示例转换为基于代码路由的对比：

基于文件版本的结构：

```
routes/
├── __root.tsx
├── index.tsx
├── about.tsx
├── posts/
│   ├── index.tsx
│   ├── $postId.tsx
├── posts.$postId.edit.tsx
├── settings/
│   ├── profile.tsx
│   ├── notifications.tsx
├── _pathlessLayout.tsx
├── _pathlessLayout/
│   ├── route-a.tsx
├── ├── route-b.tsx
├── files/
│   ├── $.tsx
```

基于代码的简化版本：

```tsx
import { createRootRoute, createRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()

const indexRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: '/',
})

const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'about',
})

const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})

const postRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
})

const postEditorRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts/$postId/edit',
})

const settingsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'settings',
})

const profileRoute = createRoute({
  getParentRoute: () => settingsRoute,
  path: 'profile',
})

const notificationsRoute = createRoute({
  getParentRoute: () => settingsRoute,
  path: 'notifications',
})

const pathlessLayoutRoute = createRoute({
  getParentRoute: () => rootRoute,
  id: 'pathlessLayout',
})

const pathlessLayoutARoute = createRoute({
  getParentRoute: () => pathlessLayoutRoute,
  path: 'route-a',
})

const pathlessLayoutBRoute = createRoute({
  getParentRoute: () => pathlessLayoutRoute,
  path: 'route-b',
})

const filesRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'files/$',
})
```

## 路由结构解析

除根路由外，所有其他路由都通过`createRoute`函数配置：

```tsx
const route = createRoute({
  getParentRoute: () => rootRoute,
  path: '/posts',
  component: PostsComponent,
})
```

其中`getParentRoute`选项是一个返回父路由的函数。

**❓❓❓ "需要为每个路由指定父路由？"**

确实如此！这种设计完全是为了实现TanStack Router强大的**类型安全**特性。若缺少父路由信息，TypeScript将无法推断路由的类型参数！

> [!IMPORTANT]
> 对于**非根路由**和**无路径布局路由**，必须配置`path`选项。该路径用于匹配URL路径以确定路由是否匹配。

路由路径配置时会自动规范化首尾斜杠（不包括索引路由路径`/`）。以下为路径规范化示例表：

| 原始路径 | 规范化路径 |
| -------- | ---------- |
| `/`      | `/`        |
| `/about` | `about`    |
| `about/` | `about`    |
| `about`  | `about`    |
| `$`      | `$`        |
| `/$`     | `$`        |
| `/$/`    | `$`        |

## 手动构建路由树

在代码中构建路由树时，仅定义父路由是不够的。必须通过将每个路由添加到其父路由的`children`数组来显式构建完整路由树，这与基于文件路由的自动构建不同。

```tsx
/* prettier-ignore */
const routeTree = rootRoute.addChildren([
  indexRoute,
  aboutRoute,
  postsRoute.addChildren([
    postsIndexRoute,
    postRoute,
  ]),
  postEditorRoute,
  settingsRoute.addChildren([
    profileRoute,
    notificationsRoute,
  ]),
  pathlessLayoutRoute.addChildren([
    pathlessLayoutARoute,
    pathlessLayoutBRoute,
  ]),
  filesRoute.addChildren([
    fileRoute,
  ]),
])
/* prettier-ignore-end */
```

但在构建路由树前，需要理解基于代码路由的核心概念。

## 基于代码路由的核心概念

实际上，基于文件的路由是代码路由的超集，它通过文件系统和代码生成抽象自动创建上述路由结构。

假设您已阅读[路由核心概念](./routing-concepts.md)指南并熟悉以下概念：

- 根路由
- 基础路由
- 索引路由
- 动态路由段
- 通配/全捕获路由
- 布局路由
- 无路径路由
- 非嵌套路由

接下来我们将展示如何在代码中创建这些路由类型。

## 根路由

创建根路由的方式与文件路由相同，调用`createRootRoute()`函数即可。不同之处在于不需要强制导出根路由（虽然可以在单个文件中构建完整路由树，但仅为演示目的）。

```tsx
// 标准根路由
import { createRootRoute } from '@tanstack/react-router'

const rootRoute = createRootRoute()

// 带上下文的根路由
import { createRootRouteWithContext } from '@tanstack/react-router'
import type { QueryClient } from '@tanstack/react-query'

export interface MyRouterContext {
  queryClient: QueryClient
}
const rootRoute = createRootRouteWithContext<MyRouterContext>()
```

关于上下文更多信息，请参阅[路由上下文](../guide/router-context.md)指南。

## 基础路由

创建基础路由只需向`createRoute`提供普通路径字符串：

```tsx
const aboutRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'about',
})
```

该路由将匹配URL路径`/about`。

## 索引路由

与文件路由使用`index`文件名不同，代码路由使用单斜杠`/`表示索引路由：

```tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
})

const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/', // 注意此处的单斜杠
})
```

此路由将匹配`/posts/`或`/posts`。

## 动态路由段

动态段的使用方式与文件路由一致。在路径段前添加`$`前缀，该参数将被捕获到路由的`loader`或`component`的`params`对象中：

```tsx
const postIdRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '$postId',
  loader: ({ params }) => fetchPost(params.postId),
  component: PostComponent,
})

function PostComponent() {
  const { postId } = postIdRoute.useParams()
  return <div>文章ID: {postId}</div>
}
```

> [!TIP]
> 若组件采用代码分割，可使用[getRouteApi函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)来避免导入路由配置即可获取类型化的`useParams()`钩子。

## 通配/全捕获路由

通配路由同样通过`$`前缀实现，捕获的参数存储在`_splat`键下：

```tsx
const filesRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'files',
})

const fileRoute = createRoute({
  getParentRoute: () => filesRoute,
  path: '$',
})
```

对于URL`/documents/hello-world`，参数对象为：

```js
{ '_splat': 'documents/hello-world' }
```

## 布局路由

布局路由通过嵌套子路由实现：

```tsx
const postsRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts',
  component: PostsLayoutComponent, // 布局组件
})

function PostsLayoutComponent() {
  return (
    <div>
      <h1>文章列表</h1>
      <Outlet />
    </div>
  )
}

const routeTree = rootRoute.addChildren([
  postsRoute.addChildren([postsIndexRoute, postsCreateRoute]),
])
```

子路由内容将渲染在`PostsLayoutComponent`布局中。

## 无路径布局路由

代码路由中通过`id`而非`path`选项创建无路径路由：

```tsx
const pathlessLayoutRoute = createRoute({
  getParentRoute: () => rootRoute,
  id: 'pathlessLayout',
  component: PathlessLayoutComponent,
})

const routeTree = rootRoute.addChildren([
  pathlessLayoutRoute.addChildren([pathlessLayoutARoute, pathlessLayoutBRoute]),
])
```

子路由内容将嵌套在无路径布局组件中渲染。

## 非嵌套路由

实现非嵌套路由需要从目标嵌套层级开始构建完整路径：

```tsx
const postEditorRoute = createRoute({
  getParentRoute: () => rootRoute,
  path: 'posts/$postId/edit', // 完整路径
})

const routeTree = rootRoute.addChildren([
  postEditorRoute, // 直接挂在根路由下
  postsRoute.addChildren([postRoute]),
])
```
