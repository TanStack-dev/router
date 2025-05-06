---
source-updated-at: 2025-03-18T21:44:40.000Z
translation-updated-at: 2025-04-05T03:14:15.000Z
title: 路由概念
---

TanStack Router 支持多种强大的路由概念，可帮助您轻松构建复杂动态的路由系统。以下将详细介绍这些核心概念。

## 路由结构剖析

除[根路由](#根路由)外，所有路由都通过`createFileRoute`函数配置，该函数在使用基于文件的路由时提供类型安全：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})
```

该函数接收一个参数——字符串形式的文件路由路径。

**❓❓❓ "需要手动传递路由文件路径？"**

是的！但请放心，这个路径会通过**TanStack Router打包插件或CLI工具自动生成维护**。当您创建、移动或重命名路由时，路径会自动更新。

路径参数的存在是为了实现TanStack Router强大的类型安全功能。没有这个路径，TypeScript就无法识别当前文件位置！（我们希望TypeScript原生支持此功能，但目前尚未实现🤷‍♂️）

## 根路由

根路由是整个路由树的顶层节点，包含所有子路由：

- 没有路径参数
- **始终**匹配
- 其`component`**始终**渲染

虽然根路由没有路径，但它拥有与其他路由相同的功能：

- 组件渲染
- 数据加载器
- 搜索参数验证
- 等等

创建根路由需调用`createRootRoute()`并导出为`Route`变量：

```tsx
// 标准根路由
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute()

// 带上下文的根路由
import { createRootRouteWithContext } from '@tanstack/react-router'
import type { QueryClient } from '@tanstack/react-query'

export interface MyRouterContext {
  queryClient: QueryClient
}
export const Route = createRootRouteWithContext<MyRouterContext>()
```

关于上下文更多信息，请参阅[路由上下文](../guide/router-context.md)指南。

## 基础路由

基础路由精确匹配特定路径，如`/about`、`/settings/notifications`等。

示例`/about`路由：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutComponent,
})

function AboutComponent() {
  return <div>关于页面</div>
}
```

基础路由简单直接，精确匹配路径并渲染对应组件。

## 索引路由

索引路由在其父路由**精确匹配且无子路由匹配**时生效。

`/posts`的索引路由示例：

```tsx
import { createFileRoute } from '@tanstack/react-router'

// 注意尾部斜杠用于标识索引路由
export const Route = createFileRoute('/posts/')({
  component: PostsIndexComponent,
})

function PostsIndexComponent() {
  return <div>请选择文章！</div>
}
```

当URL为`/posts`时匹配此路由。

## 动态路由段

以`$`开头的路径段是动态段，会将该URL部分捕获到`params`对象中。例如`/posts/123`会匹配`/posts/$postId`路由，且`params`为`{ postId: '123' }`。

这些参数可在路由配置和组件中使用：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // 在加载器中
  loader: ({ params }) => fetchPost(params.postId),
  // 在组件中
  component: PostComponent,
})

function PostComponent() {
  const { postId } = Route.useParams()
  return <div>文章ID: {postId}</div>
}
```

> 🧠 动态段可存在于路径的每一级，例如`/posts/$postId/$revisionId`会捕获所有`$`段到`params`对象。

## 通配路由

仅包含`$`的路径称为通配路由，它会捕获从`$`开始的所有剩余URL路径，并通过特殊的`_splat`属性提供：

```js
{
  '_splat': 'documents/hello-world'
}
```

> ⚠️ v1版本中为保持兼容性也支持`*`表示法，v2将移除该特性。

> 🧠 使用`$`是因为`*`在文件名和CLI工具中兼容性不佳。

## 布局路由

布局路由用于用额外组件和逻辑包装子路由，适用于：

- 为子路由提供统一布局
- 在显示子路由前强制执行数据加载
- 验证并提供搜索参数
- 提供错误回退和加载状态
- 共享上下文数据
- 等等

示例`app.tsx`布局路由：

```tsx
import { Outlet, createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/app')({
  component: AppLayoutComponent,
})

function AppLayoutComponent() {
  return (
    <div>
      <h1>应用布局</h1>
      <Outlet />
    </div>
  )
}
```

也支持目录结构：

```
routes/
├── app/
│   ├── route.tsx
│   ├── dashboard.tsx
│   ├── settings.tsx
```

## 无路径布局路由

与布局路由类似，但不需要URL路径匹配。通过前缀`_`标识无路径特性。

示例`_pathlessLayout.tsx`：

```tsx
import { Outlet, createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/_pathlessLayout')({
  component: PathlessLayoutComponent,
})

function PathlessLayoutComponent() {
  return (
    <div>
      <h1>无路径布局</h1>
      <Outlet />
    </div>
  )
}
```

> 🧠 无路径布局路由不支持动态段匹配。

## 非嵌套路由

通过在父路由段后添加`_`创建，使路由脱离父级组件树独立渲染。

示例结构：

```
routes/
├── posts.tsx
├── posts.$postId.tsx
├── posts_.$postId.edit.tsx
```

## 路径分组目录

使用`()`对路由文件进行纯组织性分组，不影响实际路由结构。

示例：

```
routes/
├── (app)/
│   ├── dashboard.tsx
│   ├── settings.tsx
├── (auth)/
│   ├── login.tsx
```

分组目录仅用于代码组织，不改变路由匹配行为。
