---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:28:37.000Z
title: 类型安全
---

TanStack Router 在设计上力求在 TypeScript 编译器和运行时的限制范围内实现最大程度的类型安全。这意味着它不仅是用 TypeScript 编写的，还能**完全推断所提供的类型，并将这些类型严格贯穿整个路由体验**。

最终，这意味着开发者**需要编写的类型更少**，并且随着代码演进可以**对代码更有信心**。

## 路由定义

### 基于文件的路由

路由是分层的，它们的定义也是如此。如果使用基于文件的路由，大部分类型安全已经自动处理好了。

### 基于代码的路由

如果直接使用 `Route` 类，需要注意如何通过 `Route` 的 `getParentRoute` 选项确保路由类型正确。因为子路由需要了解其**所有**父路由的类型。否则，你在*布局*和*无路径布局*路由中解析出的那些珍贵的搜索参数（可能来自三层以上的父路由）将会丢失在 JavaScript 的虚无中。

所以，别忘了将父路由传递给子路由！

```tsx
const parentRoute = createRoute({
  getParentRoute: () => parentRoute,
})
```

## 导出的钩子、组件和工具

为了使路由器的类型能与 `Link`、`useNavigate`、`useParams` 等顶层导出协同工作，这些类型必须渗透 TypeScript 模块边界并直接注册到库中。为此，我们在导出的 `Register` 接口上使用声明合并。

```ts
const router = createRouter({
  // ...
})

declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}
```

通过将路由器注册到模块中，你现在可以使用导出的钩子、组件和工具，并享受路由器精确类型的支持。

## 解决组件上下文问题

组件上下文是 React 和其他框架中用于向组件提供依赖项的强大工具。然而，如果上下文在组件层次结构中移动时类型发生变化，TypeScript 将无法推断这些变化。为了解决这个问题，基于上下文的钩子和组件要求你提供关于它们如何及在哪里被使用的提示。

```tsx
export const Route = createFileRoute('/posts')({
  component: PostsComponent,
})

function PostsComponent() {
  // 每个路由都有 TanStack Router 内置钩子的类型安全版本
  const params = Route.useParams()
  const search = Route.useSearch()

  // 某些钩子需要来自*整个*路由器的上下文，而不仅仅是当前路由。为了实现类型安全，
  // 我们必须传递 `from` 参数，告诉钩子我们在路由层次结构中的相对位置。
  const navigate = useNavigate({ from: Route.fullPath })
  // ... 等等
}
```

每个需要上下文提示的钩子和组件都会有一个 `from` 参数，你可以传递正在渲染的路由的 ID 或路径。

> 🧠 小技巧：如果你的组件是代码分割的，可以使用 [getRouteApi 函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper) 来避免传递 `Route.fullPath`，同时仍能访问类型化的 `useParams()` 和 `useSearch()` 钩子。

### 如果我不知道路由怎么办？如果是共享组件呢？

`from` 属性是可选的，如果不传递，路由器会尽力猜测可用的类型。通常，这意味着你会获得路由器中所有路由类型的联合类型。

### 如果传递了错误的 `from` 路径会怎样？

技术上可以传递一个满足 TypeScript 但不匹配运行时实际渲染路由的 `from` 路径。在这种情况下，每个支持 `from` 的钩子和组件都会检测你的预期是否与实际渲染的路由匹配，如果不匹配会抛出运行时错误。

### 如果我不知道路由，或者它是共享组件，无法传递 `from` 怎么办？

如果渲染的组件在多个路由间共享，或者组件不在路由内，可以传递 `strict: false` 替代 `from` 选项。这不仅会静默运行时错误，还会为你提供宽松但准确的类型。例如，在共享组件中调用 `useSearch`：

```tsx
function MyComponent() {
  const search = useSearch({ strict: false })
}
```

此时，`search` 变量将被类型化为路由器中所有路由可能的搜索参数的联合类型。

## 路由器上下文

路由器上下文极其有用，因为它是终极的层次化依赖注入。你可以向路由器及其渲染的每个路由提供上下文。随着上下文的构建，TanStack Router 会将其与路由层次结构合并，使每个路由都能访问其所有父路由的上下文。

`createRootRouteWithContext` 工厂函数创建一个具有实例化类型的新路由器，这会要求你向路由器履行相同的类型契约，并确保上下文在整个路由树中类型正确。

```tsx
const rootRoute = createRootRouteWithContext<{ whateverYouWant: true }>()({
  component: App,
})

const routeTree = rootRoute.addChildren([
  // ... 所有子路由都可以访问上下文中的 `whateverYouWant`
])

const router = createRouter({
  routeTree,
  context: {
    // 现在必须传递这个
    whateverYouWant: true,
  },
})
```

## 性能建议

随着应用规模扩大，TypeScript 检查时间自然会增加。以下是应用扩展时需要记住的几点，以保持 TS 检查时间的可控性。

### 仅推断需要的类型

客户端数据缓存（如 TanStack Query）的一个好模式是预取数据。例如，在 `loader` 中调用 `queryClient.ensureQueryData`。

```tsx
export const Route = createFileRoute('/posts/$postId/deep')({
  loader: ({ context: { queryClient }, params: { postId } }) =>
    queryClient.ensureQueryData(postQueryOptions(postId)),
  component: PostDeepComponent,
})

function PostDeepComponent() {
  const params = Route.useParams()
  const data = useSuspenseQuery(postQueryOptions(params.postId))

  return <></>
}
```

对于小型路由树，这可能看起来没问题，你可能不会注意到 TS 性能问题。但这里 TS 必须推断 loader 的返回类型，尽管它在路由中从未被使用。如果 loader 数据是复杂类型，并且许多路由以这种方式预取，可能会拖慢编辑器性能。此时，只需简单修改，让 TypeScript 推断 `Promise<void>`。

```tsx
export const Route = createFileRoute('/posts/$postId/deep')({
  loader: async ({ context: { queryClient }, params: { postId } }) => {
    await queryClient.ensureQueryData(postQueryOptions(postId))
  },
  component: PostDeepComponent,
})

function PostDeepComponent() {
  const params = Route.useParams()
  const data = useSuspenseQuery(postQueryOptions(params.postId))

  return <></>
}
```

这样 loader 数据不会被推断，类型推断会延迟到首次使用 `useSuspenseQuery` 时。

### 尽可能缩小到相关路由

考虑以下 `Link` 的用法：

```tsx
<Link to=".." search={{ page: 0 }} />
<Link to="." search={{ page: 0 }} />
```

**这些示例对 TS 性能不利**。因为 `search` 解析为所有路由的 `search` 参数的联合类型，TS 必须检查你传递给 `search` 属性的值是否与这个可能很大的联合类型兼容。随着应用规模增长，检查时间会随路由和搜索参数数量线性增加。我们已尽力优化这种情况（TypeScript 通常会缓存检查结果），但初始检查这个大联合类型的开销仍然很高。这也适用于 `params` 和其他 API，如 `useSearch`、`useParams`、`useNavigate` 等。

相反，应尝试通过 `from` 或 `to` 缩小到相关路由：

```tsx
<Link from={Route.fullPath} to=".." search={{page: 0}} />
<Link from="/posts" to=".." search={{page: 0}} />
```

记住，你可以传递联合类型给 `to` 或 `from` 以缩小目标路由范围：

```tsx
const from: '/posts/$postId/deep' | '/posts/' = '/posts/'
<Link from={from} to='..' />
```

你还可以传递分支给 `from`，仅解析该分支下所有后代路由的 `search` 或 `params`：

```tsx
const from = '/posts'
<Link from={from} to='..' />
```

`/posts` 可能是一个包含许多共享相同 `search` 或 `params` 的后代路由的分支。

### 考虑使用 `addChildren` 的对象语法

路由通常具有 `params`、`search`、`loader` 或 `context`，甚至可能引用外部依赖，这些都会增加 TS 推断的负担。对于此类应用，使用对象创建路由树比元组更高效。

`createChildren` 也可以接受对象。对于具有复杂路由和外部依赖的大型路由树，对象在 TS 类型检查时比大型元组快得多。性能提升取决于项目、外部依赖及其类型定义的方式。

```tsx
const routeTree = rootRoute.addChildren({
  postsRoute: postsRoute.addChildren({ postRoute, postsIndexRoute }),
  indexRoute,
})
```

注意，这种语法更冗长，但 TS 性能更好。在基于文件的路由中，路由树是自动生成的，因此冗长的路由树不是问题。

### 避免未缩窄的内部类型

你可能会想重用暴露的类型。例如，可能会尝试这样使用 `LinkProps`：

```tsx
const props: LinkProps = {
  to: '/posts/',
}

return (
  <Link {...props}>
)
```

**这对 TS 性能非常不利**。问题在于 `LinkProps` 没有类型参数，因此是一个非常大的类型。它包含 `search`（所有 `search` 参数的联合类型）和 `params`（所有 `params` 的联合类型）。将此对象与 `Link` 合并时，会进行这个大类型的结构比较。

相反，可以使用 `as const satisfies` 推断精确类型，而不是直接使用 `LinkProps`，以避免庞大的检查：

```tsx
const props = {
  to: '/posts/',
} as const satisfies LinkProps

return (
  <Link {...props}>
)
```

由于 `props` 不是 `LinkProps` 类型，检查会更高效，因为类型更精确。还可以通过缩窄 `LinkProps` 进一步优化类型检查：

```tsx
const props = {
  to: '/posts/',
} as const satisfies LinkProps<RegisteredRouter, string '/posts/'>

return (
  <Link {...props}>
)
```

这会更快，因为我们检查的是缩窄后的 `LinkProps` 类型。

你还可以用这种方法缩窄 `LinkProps` 类型，用作函数参数或属性：

```tsx
export const myLinkProps = [
  {
    to: '/posts',
  },
  {
    to: '/posts/$postId',
    params: { postId: 'postId' },
  },
] as const satisfies ReadonlyArray<LinkProps>

export type MyLinkProps = (typeof myLinkProps)[number]

const MyComponent = (props: { linkProps: MyLinkProps }) => {
  return <Link {...props.linkProps} />
}
```

这比直接在组件中使用 `LinkProps` 更快，因为 `MyLinkProps` 是更精确的类型。

另一种解决方案是不使用 `LinkProps`，而是通过反转控制渲染一个缩窄到特定路由的 `Link` 组件。渲染属性是反转控制给组件用户的好方法：

```tsx
export interface MyComponentProps {
  readonly renderLink: () => React.ReactNode
}

const MyComponent = (props: MyComponentProps) => {
  return <div>{props.renderLink()}</div>
}

const Page = () => {
  return <MyComponent renderLink={() => <Link to="/absolute" />} />
}
```

这个特定示例非常高效，因为我们已将导航目标的反转控制交给了组件用户。`Link` 被精确缩窄到我们想要导航的路由。
