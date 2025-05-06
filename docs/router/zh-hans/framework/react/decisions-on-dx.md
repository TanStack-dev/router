---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-04-08T00:35:37.063Z'
title: 开发体验决策
---

# 关于开发体验的决策

当人们初次使用 TanStack Router 时，通常会围绕以下主题产生许多疑问：

> 为什么必须以这种方式操作？

> 为什么采用这种实现方式而不是另一种？

> 我已经习惯某种方式，为什么要改变？

这些都是合理的问题。大多数情况下，人们习惯于使用功能相似的路由库，这些库具有相似的 API、相似的概念和相似的操作方式。

但 TanStack Router 与众不同。它不是普通的路由库，不是普通的状态管理工具，它与任何常规解决方案都不同。

## TanStack Router 的起源故事

需要明确的是，TanStack Router 源于 [Nozzle.io](https://nozzle.io) 对客户端路由解决方案的需求，该方案需要提供一流的 _URL 搜索参数_ 体验，同时不妥协于复杂仪表盘所需的 **_类型安全_** 特性。

因此，从 TanStack Router 诞生之初，其设计的每个方面都经过精心考虑，以确保其类型安全和开发体验无与伦比。

## TanStack Router 如何实现这一目标？

> TypeScript！TypeScript！TypeScript！

TanStack Router 的每个方面都设计为尽可能类型安全，这是通过充分利用 TypeScript 的类型系统实现的。这涉及使用一些非常高级和复杂的类型、类型推断等功能，以确保开发体验尽可能流畅。

但为了实现这一点，我们不得不做出一些偏离路由领域常规的决策。

1. [**路由配置样板代码？**](#1-为什么路由器的配置采用这种方式)：您必须以允许 TypeScript 尽可能推断路由类型的方式定义路由。
2. [**路由器的 TypeScript 模块声明？**](#2-为类型推断声明路由器实例)：您需要使用 TypeScript 的模块声明将 `Router` 实例传递给应用程序的其余部分。
3. [**为什么推荐基于文件的路由而非基于代码的路由？**](#3-为什么基于文件的路由是定义路由的首选方式)：我们推荐基于文件的路由作为定义路由的首选方式。

> 简而言之；TanStack Router 开发体验中的所有设计决策都是为了在不牺牲路由配置的控制性、灵活性和可维护性的前提下，提供一流的类型安全体验。

## 1. 为什么路由器的配置采用这种方式？

当您希望充分利用 TypeScript 的推断功能时，会很快意识到 _泛型_ 是您最好的朋友。因此，TanStack Router 处处使用泛型，以确保尽可能推断路由的类型。

这意味着您必须以允许 TypeScript 尽可能推断路由类型的方式定义路由。

> 可以使用 JSX 定义路由吗？

使用 JSX 定义路由是 **不可能的**，因为 TypeScript 无法推断路由器的路由配置类型。

```tsx
// ⛔️ 这种方式不可行
function App() {
  return (
    <Router>
      <Route path="/posts" component={PostsPage} />
      <Route path="/posts/$postId" component={PostIdPage} />
      {/* ... */}
    </Router>
    // ^? TypeScript 无法推断此配置中的路由
  )
}
```

由于这意味着您必须手动键入 `<Link>` 组件的 `to` 属性，并且在运行时才能捕获任何错误，因此这不是一个可行的选择。

> 或许可以将路由定义为嵌套对象树？

```tsx
// ⛔️ 这个文件会不断膨胀...
const router = createRouter({
  routes: {
    posts: {
      component: PostsPage, // /posts
      children: {
        $postId: {
          component: PostIdPage, // /posts/$postId
        },
      },
    },
    // ...
  },
})
```

乍一看，这似乎是个好主意。可以一次性可视化整个路由层次结构。但这种方法有几个重大缺点，使其不适用于大型应用程序：

- **可扩展性差**：随着应用程序增长，树会变得越来越大，越来越难以管理。由于所有内容都在一个文件中定义，维护会变得非常困难。
- **不利于代码分割**：您必须手动分割每个组件，然后将其传递到路由的 `component` 属性中，进一步使路由配置复杂化，导致路由配置文件不断膨胀。

当您开始使用路由器的更多功能时，如嵌套上下文、加载器、搜索参数验证等，情况只会变得更糟。

> 那么，定义路由的最佳方式是什么？

我们发现的最佳方式是将路由配置的定义抽象到路由树之外。然后将路由配置拼接成一个统一的路由树，传递给 `createRouter` 函数。

您可以阅读更多关于 [基于代码的路由](./routing/code-based-routing.md) 的内容，了解如何以这种方式定义路由。

> [!TIP]
> 觉得基于代码的路由有些繁琐？了解为什么 [基于文件的路由](#3-为什么基于文件的路由是定义路由的首选方式) 是定义路由的首选方式。

## 2. 为类型推断声明路由器实例

> 为什么必须声明 `Router`？

> 这些声明对我来说太复杂了...

一旦将路由构建成树并传递到路由器实例（使用 `createRouter`）中，且所有泛型正常工作，您需要以某种方式将这些信息传递给应用程序的其余部分。

我们考虑过两种方法：

1. **导入**：您可以从创建路由器的文件中导入 `Router` 实例，并直接在组件中使用它。

```tsx
import { router } from '@/src/app'
export const PostsIdLink = () => {
  return (
    <Link<typeof router> to="/posts/$postId" params={{ postId: '123' }}>
      转到文章 123
    </Link>
  )
}
```

这种方法的缺点是必须将整个 `Router` 实例导入到每个需要使用它的文件中。这会导致打包体积增加，管理起来也很麻烦，随着应用程序增长和使用路由器功能的增多，情况只会变得更糟。

2. **模块声明**：您可以使用 TypeScript 的模块声明将 `Router` 实例声明为一个模块，可以在应用程序的任何地方用于类型推断，而无需导入它。

您只需在应用程序中执行一次此操作。

```tsx
// src/app.tsx
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

然后您可以在应用程序的任何地方受益于其自动完成功能，而无需导入它。

```tsx
export const PostsIdLink = () => {
  return (
    <Link
      to="/posts/$postId"
      // ^? TypeScript 将为您自动完成
      params={{ postId: '123' }} // 这个也是！
    >
      转到文章 123
    </Link>
  )
}
```

我们选择了 **模块声明**，因为这是我们发现的最具可扩展性和可维护性的方法，开销和样板代码最少。

## 3. 为什么基于文件的路由是定义路由的首选方式？

> 为什么文档推荐基于文件的路由？

> 我习惯在单个文件中定义路由，为什么要改变？

您会很快注意到，TanStack Router 文档中推荐 **基于文件的路由** 作为定义路由的首选方法。这是因为我们发现基于文件的路由是最具可扩展性和可维护性的路由定义方式。

> [!TIP]
> 继续之前，请确保您充分理解 [基于代码的路由](./routing/code-based-routing.md) 和 [基于文件的路由](./routing/file-based-routing.md)。

如前所述，TanStack Router 专为需要高度类型安全和可维护性的复杂应用程序设计。为了实现这一点，路由器的配置采用了精确的方式，以允许 TypeScript 尽可能推断路由的类型。

与 _基本_ 应用程序设置的关键区别在于，TanStack Router 的路由配置需要为 `getParentRoute` 提供一个函数，该函数返回当前路由的父路由。

```tsx
import { createRoute } from '@tanstack/react-router'
import { postsRoute } from './postsRoute'

export const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
})
```

在此阶段，这样做是为了让 `postsIndexRoute` 的定义能够知道其在路由树中的位置，并正确推断父路由返回的 `context`、`path params`、`search params` 的类型。错误定义 `getParentRoute` 函数意味着子路由无法正确推断父路由的属性。

因此，这是路由配置的关键部分，如果操作不当，会导致失败。

但这只是设置基本应用程序的一部分。TanStack Router 要求所有路由（包括根路由）拼接成一个 **_路由树_**，以便在声明模块上的 `Router` 实例以进行类型推断之前，可以将其传递给 `createRouter` 函数。这是路由配置的另一个关键部分，如果操作不当，会导致失败。

> 🤯 如果这个路由树位于一个拥有约 40-50 个路由的应用程序的独立文件中，它可以轻松增长到 700 多行。

```tsx
const routeTree = rootRoute.addChildren([
  postsRoute.addChildren([postsIndexRoute, postsIdRoute]),
])
```

随着您开始使用路由器的更多功能，如嵌套上下文、加载器、搜索参数验证等，这种复杂性只会增加。因此，在单个文件中定义路由不再可行。用户最终会构建自己的 _半一致_ 方式在多文件中定义路由。这可能导致路由配置的不一致和错误。

最后是代码分割的问题。随着应用程序增长，您会希望分割代码以减少应用程序的初始打包体积。当您在单个文件甚至多个文件中定义路由时，管理起来可能会有些头疼。

```tsx
import { createRoute, lazyRouteComponent } from '@tanstack/react-router'
import { postsRoute } from './postsRoute'

export const postsIndexRoute = createRoute({
  getParentRoute: () => postsRoute,
  path: '/',
  component: lazyRouteComponent(() => import('../page-components/posts/index')),
})
```

所有这些样板代码，无论对于提供一流的类型推断体验多么重要，都可能让人感到不知所措，并导致路由配置的不一致和错误。

... 而这个示例配置仅用于渲染单个代码分割路由。想象一下为 40-50 个路由执行此操作。现在请记住，您还没有触及 `context`、`loaders`、`search param validation` 和路由器的其他功能 🤕。

> 那么，为什么基于文件的路由是首选方式？

TanStack Router 的基于文件的路由旨在解决所有这些问题。它允许您以可预测的方式定义路由，易于管理和维护，并且随着应用程序的增长而具有可扩展性。

基于文件的路由方法由 TanStack Router 打包器插件提供支持。它执行三个基本任务，解决了使用基于代码的路由时的路由配置痛点：

1. **路由配置样板代码**：它为您的路由配置生成样板代码。
2. **路由树拼接**：它将您的路由配置拼接成一个统一的路由树。在后台，它还会正确更新路由配置，定义 `getParentRoute` 函数以匹配路由与其父路由。
3. **代码分割**：它会自动分割您的路由内容组件，并使用正确的组件更新路由配置。此外，在运行时，它确保在访问路由时加载正确的组件。

让我们看看前面示例的路由配置在使用基于文件的路由时的样子。

```tsx
// src/routes/posts/index.ts
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/')({
  component: () => 'Posts index component goes here!!!',
})
```

就这样！无需担心定义 `getParentRoute` 函数、拼接路由树或分割代码组件。TanStack Router 打包器插件会为您处理所有这些。

TanStack Router 打包器插件在任何时候都不会剥夺您对路由配置的控制权。它设计得尽可能灵活，允许您以适合应用程序的方式定义路由，同时减少路由配置的样板代码和复杂性。

查看 [基于文件的路由](./routing/file-based-routing.md) 和 [代码分割](./routing/code-splitting.md) 的指南，深入了解它们在 TanStack Router 中的工作原理。
