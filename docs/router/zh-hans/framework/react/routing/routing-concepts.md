---
source-updated-at: '2025-05-01T22:17:05.000Z'
translation-updated-at: '2025-05-06T22:18:29.852Z'
title: 路由概念
---

TanStack Router 支持多种强大的路由概念，让您能够轻松构建复杂且动态的路由系统。

这些概念各自实用且强大，我们将在后续章节中逐一深入探讨。

## 路由结构剖析

除[根路由](#the-root-route)外，所有其他路由都通过 `createFileRoute` 函数配置，该函数在使用基于文件的路由时提供类型安全：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})
```

`createFileRoute` 函数接收一个参数，即文件路由的路径字符串。

**❓❓❓ "等等，需要手动将路由文件的路径传给 `createFileRoute`？"**

是的！但请放心，这个路径**会通过 TanStack Router Bundler Plugin 或 Router CLI 自动生成和管理**。因此，当您创建新路由、移动或重命名路由时，路径会自动更新。

这个路径名的存在与 TanStack Router 强大的类型安全特性密切相关。没有这个路径名，TypeScript 将无法识别当前文件！（我们希望 TypeScript 能内置此功能，但目前尚未实现 🤷‍♂️）

## 根路由

根路由是整个路由树的最顶层路由，封装了所有其他子路由。

- 它没有路径
- **始终**匹配
- 其 `component` **始终**渲染

尽管没有路径，根路由仍拥有与其他路由相同的功能，包括：

- 组件
- 加载器
- 搜索参数验证
- 等等

要创建根路由，调用 `createRootRoute()` 函数并在路由文件中将其导出为 `Route` 变量：

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

要了解更多关于 TanStack Router 上下文的信息，请参阅[路由上下文](../guide/router-context.md)指南。

## 基础路由

基础路由匹配特定路径，例如 `/about`、`/settings`、`/settings/notifications` 都是基础路由，因为它们精确匹配路径。

来看一个 `/about` 路由示例：

```tsx
// about.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutComponent,
})

function AboutComponent() {
  return <div>About</div>
}
```

基础路由简单直接。它们精确匹配路径并渲染提供的组件。

## 索引路由

索引路由专门在其父路由**精确匹配且无子路由匹配**时生效。

来看一个 `/posts` URL 的索引路由示例：

```tsx
// posts.index.tsx
import { createFileRoute } from '@tanstack/react-router'

// 注意尾部斜杠，用于定位索引路由
export const Route = createFileRoute('/posts/')({
  component: PostsIndexComponent,
})

function PostsIndexComponent() {
  return <div>请选择一篇文章！</div>
}
```

当 URL 精确为 `/posts` 时，此路由将被匹配。

## 动态路由段

以 `$` 开头后跟标签的路由路径段是动态的，并将该部分 URL 捕获到 `params` 对象中供应用程序使用。例如，路径名 `/posts/123` 将匹配 `/posts/$postId` 路由，且 `params` 对象为 `{ postId: '123' }`。

这些参数可在路由配置和组件中使用！来看一个 `posts.$postId.tsx` 路由示例：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  // 在加载器中
  loader: ({ params }) => fetchPost(params.postId),
  // 或在组件中
  component: PostComponent,
})

function PostComponent() {
  // 在组件中！
  const { postId } = Route.useParams()
  return <div>文章 ID: {postId}</div>
}
```

> � 动态段在路径的**每个**段都有效。例如，您可以有一个路径为 `/posts/$postId/$revisionId` 的路由，每个 `$` 段都会被捕获到 `params` 对象中。

## 通配/全捕获路由

仅包含 `$` 作为路径的路由称为“通配”路由，因为它**始终**捕获从 `$` 到 URL 路径名末尾的**任何**剩余部分。捕获的路径名随后可在 `params` 对象中通过特殊的 `_splat` 属性获取。

例如，以 `files/$` 路径为目标的路由是通配路由。如果 URL 路径名为 `/files/documents/hello-world`，`params` 对象将在特殊的 `_splat` 属性下包含 `documents/hello-world`：

```js
{
  '_splat': 'documents/hello-world'
}
```

> ⚠️ 在路由器的 v1 版本中，通配路由也使用 `*` 而非 `_splat` 键以保持向后兼容性。这将在 v2 中移除。

> � 为什么使用 `$`？得益于像 Remix 这样的工具，我们了解到尽管 `*` 是最常见的通配符表示字符，但它们与文件名或 CLI 工具的兼容性不佳，因此我们决定改用 `$`。

## 布局路由

布局路由用于通过额外的组件和逻辑包装子路由。它们在以下场景中非常有用：

- 用布局组件包装子路由
- 在显示任何子路由前强制执行 `loader` 要求
- 验证并向子路由提供搜索参数
- 为子路由提供错误组件或待处理元素的回退
- 向所有子路由提供共享上下文
- 以及更多！

来看一个名为 `app.tsx` 的布局路由示例：

```
routes/
├── app.tsx
├── app.dashboard.tsx
├── app.settings.tsx
```

在上面的树结构中，`app.tsx` 是一个布局路由，包装了两个子路由 `app.dashboard.tsx` 和 `app.settings.tsx`。

此树结构用于通过布局组件包装子路由：

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

下表展示了基于 URL 将渲染的组件：

| URL 路径         | 组件                     |
| ---------------- | ------------------------ |
| `/`              | `<Index>`                |
| `/app/dashboard` | `<AppLayout><Dashboard>` |
| `/app/settings`  | `<AppLayout><Settings>`  |

由于 TanStack Router 支持混合扁平化和目录路由，您还可以通过目录中的布局路由来表达应用程序的路由：

```
routes/
├── app/
│   ├── route.tsx
│   ├── dashboard.tsx
│   ├── settings.tsx
```

在此嵌套树中，`app/route.tsx` 文件是布局路由的配置，包装了两个子路由 `app/dashboard.tsx` 和 `app/settings.tsx`。

布局路由还允许您为动态路由段强制执行组件和加载器逻辑：

```
routes/
├── app/users/
│   ├── $userId/
|   |   ├── route.tsx
|   |   ├── index.tsx
|   |   ├── edit.tsx
```

## 无路径布局路由

与[布局路由](#layout-routes)类似，无路径布局路由用于通过额外的组件和逻辑包装子路由。然而，无路径布局路由不需要在 URL 中匹配 `path`，用于在不要求 URL 中匹配 `path` 的情况下包装子路由。

无路径布局路由通过前缀下划线 (`_`) 表示它们是“无路径”的。

> 🧠 `_` 前缀后的路径部分用作路由的 ID，这是必需的，因为每个路由必须唯一可识别，尤其是在使用 TypeScript 时，以避免类型错误并有效实现自动补全。

来看一个名为 `_pathlessLayout.tsx` 的路由示例：

```
routes/
├── _pathlessLayout.tsx
├── _pathlessLayout.a.tsx
├── _pathlessLayout.b.tsx
```

在上面的树结构中，`_pathlessLayout.tsx` 是一个无路径布局路由，包装了两个子路由 `_pathlessLayout.a.tsx` 和 `_pathlessLayout.b.tsx`。

`_pathlessLayout.tsx` 路由用于通过无路径布局组件包装子路由：

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

下表展示了基于 URL 将渲染的组件：

| URL 路径 | 组件                  |
| -------- | --------------------- |
| `/`      | `<Index>`             |
| `/a`     | `<PathlessLayout><A>` |
| `/b`     | `<PathlessLayout><B>` |

由于 TanStack Router 支持混合扁平化和目录路由，您还可以通过目录中的无路径布局路由来表达应用程序的路由：

```
routes/
├── _pathlessLayout/
│   ├── route.tsx
│   ├── a.tsx
│   ├── b.tsx
```

然而，与布局路由不同，由于无路径布局路由不基于 URL 路径段匹配，这意味着这些路由不支持在其路径中使用[动态路由段](#dynamic-route-segments)，因此无法在 URL 中匹配。

这意味着您不能这样做：

```
routes/
├── _$postId/ ❌
│   ├── ...
```

而应该这样做：

```
routes/
├── $postId/
├── _postPathlessLayout/ ✅
│   ├── ...
```

## 非嵌套路由

非嵌套路由可通过在父文件路由段后添加 `_` 后缀创建，用于**解除**路由与父级的嵌套关系并渲染其自己的组件树。

考虑以下扁平路由树：

```
routes/
├── posts.tsx
├── posts.$postId.tsx
├── posts_.$postId.edit.tsx
```

下表展示了基于 URL 将渲染的组件：

| URL 路径          | 组件                         |
| ----------------- | ---------------------------- |
| `/posts`          | `<Posts>`                    |
| `/posts/123`      | `<Posts><Post postId="123">` |
| `/posts/123/edit` | `<PostEditor postId="123">`  |

- `posts.$postId.tsx` 路由正常嵌套在 `posts.tsx` 路由下，将渲染 `<Posts><Post>`。
- `posts_.$postId.edit.tsx` 路由**不共享**与其他路由相同的 `posts` 前缀，因此将被视为顶级路由并渲染 `<PostEditor>`。

## 从路由中排除文件和文件夹

文件和文件夹可通过在文件名前添加 `-` 前缀从路由生成中排除。这使您能够在路由目录中协同定位逻辑。

考虑以下路由树：

```
routes/
├── posts.tsx
├── -posts-table.tsx // 👈🏼 被忽略
├── -components/ // 👈🏼 被忽略
│   ├── header.tsx // 👈🏼 被忽略
│   ├── footer.tsx // 👈🏼 被忽略
│   ├── ...
```

我们可以从被排除的文件中导入到 posts 路由中

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { PostsTable } from './-posts-table'
import { PostsHeader } from './-components/header'
import { PostsFooter } from './-components/footer'

export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  component: PostComponent,
})

function PostComponent() {
  const posts = Route.useLoaderData()

  return (
    <div>
      <PostsHeader />
      <PostsTable posts={posts} />
      <PostsFooter />
    </div>
  )
}
```

被排除的文件不会添加到 `routeTree.gen.ts` 中。

## 无路径路由组目录

无路径路由组目录使用 `()` 作为将路由文件分组的方式，无论其路径如何。它们纯粹是组织性的，不会以任何方式影响路由树或组件树。

```
routes/
├── index.tsx
├── (app)/
│   ├── dashboard.tsx
│   ├── settings.tsx
│   ├── users.tsx
├── (auth)/
│   ├── login.tsx
│   ├── register.tsx
```

在上面的示例中，`app` 和 `auth` 目录纯粹是组织性的，不会以任何方式影响路由树或组件树。它们用于将相关路由分组以便于导航和组织。

下表展示了基于 URL 将渲染的组件：

| URL 路径     | 组件          |
| ------------ | ------------- |
| `/`          | `<Index>`     |
| `/dashboard` | `<Dashboard>` |
| `/settings`  | `<Settings>`  |
| `/users`     | `<Users>`     |
| `/login`     | `<Login>`     |
| `/register`  | `<Register>`  |

如您所见，`app` 和 `auth` 目录纯粹是组织性的，不会以任何方式影响路由树或组件树。
