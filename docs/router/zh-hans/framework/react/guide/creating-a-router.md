---
source-updated-at: 2025-02-25T23:33:22.000Z
translation-updated-at: 2025-04-05T03:22:16.000Z
title: 创建路由器
---

## `Router` 类

当你准备开始使用路由器时，需要创建一个新的 `Router` 实例。该实例是 TanStack Router 的核心大脑，负责管理路由树、匹配路由以及协调导航和路由过渡。它同时也是配置路由器全局设置的地方。

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
})
```

## 路由树

你会很快注意到 `Router` 构造函数需要一个 `routeTree` 选项。这是路由器用来匹配路由和渲染组件的路由树。

无论你使用[基于文件的路由](../routing/file-based-routing.md)还是[基于代码的路由](../routing/code-based-routing.md)，都需要将路由树传递给 `createRouter` 函数：

### 文件系统路由树

如果使用我们推荐的基于文件的路由，那么生成的路由树文件可能位于默认的 `src/routeTree.gen.ts` 位置。如果使用了自定义位置，则需要从该位置导入路由树。

```tsx
import { routeTree } from './routeTree.gen'
```

### 基于代码的路由树

如果使用基于代码的路由，那么你可能通过根路由的 `addChildren` 方法手动创建了路由树：

```tsx
const routeTree = rootRoute.addChildren([
  // ...
])
```

## 路由类型安全

> [!IMPORTANT]
> 不要跳过本节！⚠️

TanStack Router 为 TypeScript 提供了出色的支持，甚至包括你意想不到的功能，比如直接从库中裸导入！为了实现这一点，你必须使用 TypeScript 的[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)功能注册路由器的类型。具体做法是通过扩展 `@tanstack/react-router` 上的 `Register` 接口，添加一个 `router` 属性，其类型为你的 `router` 实例：

```tsx
declare module '@tanstack/react-router' {
  interface Register {
    // 这会推断路由器的类型，并在整个项目中注册它
    router: typeof router
  }
}
```

注册路由器后，你将在整个项目中获得与路由相关的类型安全。

## 404 未找到路由

如之前指南中所承诺的，我们现在将介绍 `notFoundRoute` 选项。该选项用于配置当没有找到其他合适匹配时渲染的路由。这对于渲染 404 页面或重定向到默认路由非常有用。

如果你使用基于文件或基于代码的路由，那么需要在 `createRootRoute` 中添加一个 `notFoundComponent` 键：

```tsx
export const Route = createRootRoute({
  component: () => (
    // ...
  ),
  notFoundComponent: () => <div>404 未找到</div>,
});
```

## 其他选项

还有许多其他选项可以传递给 `Router` 构造函数。你可以在[API 参考](../api/router/RouterOptionsType.md)中找到完整的列表。
