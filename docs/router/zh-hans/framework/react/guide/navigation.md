---
source-updated-at: 2025-03-09T10:56:27.000Z
translation-updated-at: 2025-04-05T03:26:00.000Z
title: 导航
---

## 万物皆相对

无论你是否使用显式的相对路径语法（`../../somewhere`），应用内的所有导航本质上都是**相对的**。每当点击链接或执行命令式导航调用时，总会存在一个**起点**路径和一个**目标**路径，这意味着你始终是在**从**某个路由**跳转至**另一个路由。

TanStack Router 在设计所有导航时都秉持着相对导航的核心概念，因此你会在 API 中频繁看到两个属性：

- `from` - 起点路由路径
- `to` - 目标路由路径

> ⚠️ 如果未提供 `from` 路由路径，路由器会假设你从根路由 `/` 开始导航，此时仅自动补全绝对路径。毕竟，你需要知道起点才能确定去向 😉。

## 统一的导航 API

TanStack Router 中所有导航和路由匹配 API 都采用相同的核心接口，仅因 API 不同存在细微差异。这意味着你只需学习一次导航和路由匹配，就能在整个库中使用相同的语法和概念。

### `ToOptions` 接口

这是所有导航和路由匹配 API 共用的核心 `ToOptions` 接口：

```ts
type ToOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths<TRouteTree> | string = string,
  TTo extends string = '',
> = {
  // `from` 是可选的路由ID或路径。若不提供，则仅自动补全绝对路径并保持类型安全。通常为了方便会传入当前渲染路由的 route.fullPath。若不确定起点路由，请留空并使用绝对路径或不安全的相对路径。
  from: string
  // `to` 可以是绝对路径，或是基于 `from` 选项的相对路径。⚠️ 不要在 `to` 选项中直接拼接路径参数、哈希或搜索参数，应使用 `params`、`search` 和 `hash` 选项。
  to: string
  // `params` 可以是待插入 `to` 选项的路径参数对象，或是接收旧参数返回新参数的函数。这是将动态参数插入最终URL的唯一方式。根据 `from` 和 `to` 路由，可能需要提供部分或全部路径参数。TypeScript 会提示必需参数。
  params:
    | Record<string, unknown>
    | ((prevParams: Record<string, unknown>) => Record<string, unknown>)
  // `search` 可以是查询参数对象，或是接收旧搜索参数返回新参数的函数。根据路由可能需要提供部分或全部查询参数。TypeScript 会提示必需搜索参数。
  search:
    | Record<string, unknown>
    | ((prevSearch: Record<string, unknown>) => Record<string, unknown>)
  // `hash` 可以是字符串，或是接收旧哈希值返回新值的函数。
  hash?: string | ((prevHash: string) => string)
  // `state` 可以是状态对象，或是接收旧状态返回新状态的函数。状态存储在 history API 中，适合传递不想永久保存在URL搜索参数中的数据。
  state?:
    | Record<string, any>
    | ((prevState: Record<string, unknown>) => Record<string, unknown>)
}
```

> 🧠 每个路由对象都有 `to` 属性，可作为任何导航或路由匹配 API 的 `to` 值。这能帮助你避免使用纯字符串，转而使用类型安全的路由引用：

```tsx
import { Route as aboutRoute } from './routes/about.tsx'

function Comp() {
  return <Link to={aboutRoute.to}>关于</Link>
}
```

### `NavigateOptions` 接口

这是扩展自 `ToOptions` 的核心 `NavigateOptions` 接口，所有执行实际导航的 API 都会使用该接口：

```ts
export type NavigateOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths<TRouteTree> | string = string,
  TTo extends string = '',
> = ToOptions<TRouteTree, TFrom, TTo> & {
  // `replace` 决定导航是否替换当前历史记录而非新增记录
  replace?: boolean
  // `resetScroll` 决定导航提交后是否重置滚动位置至 0,0
  resetScroll?: boolean
  // `hashScrollIntoView` 决定是否将匹配哈希的ID元素滚动至视口
  hashScrollIntoView?: boolean | ScrollIntoViewOptions
  // `viewTransition` 决定是否及如何调用 document.startViewTransition()
  viewTransition?: boolean | ViewTransitionOptions
  // `ignoreBlocker` 决定是否忽略可能阻止导航的拦截器
  ignoreBlocker?: boolean
  // `reloadDocument` 决定是否触发整页加载而非SPA导航
  reloadDocument?: boolean
  // `href` 可替代 `to` 直接导航至完整构建的href（如外部链接）
  href?: string
}
```

### `LinkOptions` 接口

任何实际 `<a>` 标签都会使用扩展自 `NavigateOptions` 的 `LinkOptions` 接口：

```tsx
export type LinkOptions<
  TRouteTree extends AnyRoute = AnyRoute,
  TFrom extends RoutePaths<TRouteTree> | string = string,
  TTo extends string = '',
> = NavigateOptions<TRouteTree, TFrom, TTo> & {
  // 标准锚点标签的target属性
  target?: HTMLAnchorElement['target']
  // 默认为 `{ exact: false, includeHash: false }`
  activeOptions?: {
    exact?: boolean
    includeHash?: boolean
    includeSearch?: boolean
    explicitUndefined?: boolean
  }
  // 设置后会在悬停时预加载目标路由并缓存指定毫秒数
  preload?: false | 'intent'
  // 延迟意图预加载的毫秒数。若在延迟前取消意图，则取消预加载
  preloadDelay?: number
  // 为true时不渲染href属性
  disabled?: boolean
}
```

## 导航 API

了解相对导航和所有接口后，我们来看看可用的各类导航 API：

- `<Link>` 组件
  - 生成带有有效 `href` 的真实 `<a>` 标签，支持点击或 cmd/ctrl + 点击新标签页打开
- `useNavigate()` 钩子
  - 应优先使用 `Link` 组件，但在副作用中需要命令式导航时，`useNavigate` 返回的函数可立即执行客户端导航
- `<Navigate>` 组件
  - 不渲染内容，直接执行客户端导航
- `Router.navigate()` 方法
  - 最强大的导航 API，类似 `useNavigate` 但可在任何能访问路由器的位置使用

⚠️ 这些 API 都不能替代服务端重定向。若需在应用挂载前立即重定向，请使用服务端重定向而非客户端导航。

## `<Link>` 组件

`Link` 是应用内导航最常用的方式。它渲染真实的 `<a>` 标签，支持标准属性如 `target` 等。除 [`LinkOptions`](#linkoptions-interface) 接口外，还支持以下属性：

```tsx
export type LinkProps<
  TFrom extends RoutePaths<RegisteredRouter['routeTree']> | string = string,
  TTo extends string = '',
> = LinkOptions<RegisteredRouter['routeTree'], TFrom, TTo> & {
  // 返回链接激活状态额外属性的函数（样式会合并，类名会拼接）
  activeProps?:
    | FrameworkHTMLAnchorTagAttributes
    | (() => FrameworkHTMLAnchorAttributes)
  // 返回链接非激活状态额外属性的函数
  inactiveProps?:
    | FrameworkHTMLAnchorAttributes
    | (() => FrameworkHTMLAnchorAttributes)
}
```

### 绝对链接

创建静态链接示例：

```tsx
import { Link } from '@tanstack/react-router'

const link = <Link to="/about">关于</Link>
```

### 动态链接

带动态段落的链接示例：

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
  >
    博客文章
  </Link>
)
```

动态段落参数通常为 `string` 类型，但也可在路由选项中解析为其他类型，类型检查会在编译时进行。

### 相对链接

默认所有链接都是绝对的。若要创建相对于当前路由的链接，需提供 `from` 路径：

```tsx
const postIdRoute = createRoute({
  path: '/blog/post/$postId',
})

const link = (
  <Link from={postIdRoute.fullPath} to="../categories">
    分类
  </Link>
)
```

通常建议使用 `route.fullPath` 作为 `from` 路径以保证重构时的可维护性。

### 搜索参数链接

通过搜索参数传递额外上下文：

```tsx
const link = (
  <Link
    to="/search"
    search={{
      query: 'tanstack',
    }}
  >
    搜索
  </Link>
)
```

更新单个搜索参数而不影响其他参数：

```tsx
const link = (
  <Link
    to="."
    search={(prev) => ({
      ...prev,
      page: prev.page + 1,
    })}
  >
    下一页
  </Link>
)
```

### 搜索参数类型安全

搜索参数是高度动态的状态管理机制，必须确保类型安全。后续章节将详细介绍验证和类型安全等特性。

### 哈希链接

跳转至页面特定区域的示例：

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
    hash="section-1"
  >
    第一节
  </Link>
)
```

### 激活与非激活属性

`Link` 组件支持 `activeProps` 和 `inactiveProps` 来分别设置不同状态的额外属性。除样式和类名外，这些属性会覆盖原始属性。

示例：

```tsx
const link = (
  <Link
    to="/blog/post/$postId"
    params={{
      postId: 'my-first-blog-post',
    }}
    activeProps={{
      style: {
        fontWeight: 'bold',
      },
    }}
  >
    第一节
  </Link>
)
```

### `data-status` 属性

`Link` 组件在激活状态时会添加 `data-status="active"` 属性，便于通过数据属性进行样式控制。

### 激活选项

`activeOptions` 属性提供多种确定链接激活状态的配置：

```tsx
export interface ActiveOptions {
  // 为true时要求完全匹配（不包含子路由）
  exact?: boolean
  // 为true时要求哈希匹配
  includeHash?: boolean
  // 为true时要求搜索参数包含匹配
  includeSearch?: boolean
  // 修改includeSearch行为：显式undefined的属性必须不存在于当前URL
  explicitUndefined?: boolean
}
```

默认检查路径名前缀匹配，若提供搜索参数则要求包含匹配，默认不检查哈希。

例如在 `/blog/post/my-first-blog-post` 路由下，以下链接会激活：

```tsx
const link1 = (
  <Link to="/blog/post/$postId" params={{ postId: 'my-first-blog-post' }}>
    博客文章
  </Link>
)
const link2 = <Link to="/blog/post">博客文章</Link>
const link3 = <Link to="/blog">博客文章</Link>
```

而以下链接不会激活：

```tsx
const link4 = (
  <Link to="/blog/post/$postId" params={{ postId: 'my-second-blog-post' }}>
    博客文章
  </Link>
)
```

对于需要精确匹配的链接（如首页），可设置 `exact: true`：

````tsx
const link = (
  <Link to="/" activeOptions={{ exact: true }}>
    首页
  </Link>
)
```- 若希望在匹配时包含哈希值，可传入 `includeHash: true` 选项
- 若**不**希望在匹配时包含搜索参数，可传入 `includeSearch: false` 选项

### 向子元素传递 `isActive`

`Link` 组件接受子元素为函数的形式，允许你将 `isActive` 属性传递给子元素。例如，可以根据父级链接的激活状态来设置子组件的样式：

```tsx
const link = (
  <Link to="/blog/post">
    {({ isActive }) => {
      return (
        <>
          <span>My Blog Post</span>
          <icon className={isActive ? 'active' : 'inactive'} />
        </>
      )
    }}
  </Link>
)
````

### 链接预加载

`Link` 组件支持在用户意图（如悬停或触摸开始）时自动预加载路由。这可以通过路由器的默认配置（后续会详细介绍）或向 `Link` 组件传递 `preload='intent'` 属性来实现。示例如下：

```tsx
const link = (
  <Link to="/blog/post/$postId" preload="intent">
    Blog Post
  </Link>
)
```

启用预加载后，如果异步路由依赖加载较快，这一简单技巧可以显著提升应用的感知性能。

更棒的是，如果使用像 `@tanstack/query` 这样的缓存优先库，预加载的路由会被保留，当用户稍后导航到该路由时，可以直接使用缓存数据，同时后台重新验证（stale-while-revalidate）。

### 链接预加载超时

预加载还支持配置超时时间，决定用户悬停多久后触发基于意图的预加载。默认超时为 50 毫秒，但你可以通过向 `Link` 组件传递 `preloadTimeout` 属性来自定义等待时间（单位为毫秒）：

```tsx
const link = (
  <Link to="/blog/post/$postId" preload="intent" preloadTimeout={100}>
    Blog Post
  </Link>
)
```

## `useNavigate`

> ⚠️ 由于 `Link` 组件内置了对 `href`、Cmd/Ctrl + 点击以及激活/非激活状态的支持，建议在用户可交互的场景（如链接、按钮）中使用 `Link` 组件而非 `useNavigate`。但在某些情况下（如异步操作成功后的导航），`useNavigate` 仍是必要的。

`useNavigate` 钩子返回一个可调用的 `navigate` 函数，用于命令式导航。它非常适合在副作用（如异步操作成功）中触发导航。示例如下：

```tsx
function Component() {
  const navigate = useNavigate({ from: '/posts/$postId' })

  const handleSubmit = async (e: FrameworkFormEvent) => {
    e.preventDefault()

    const response = await fetch('/posts', {
      method: 'POST',
      body: JSON.stringify({ title: 'My First Post' }),
    })

    const { id: postId } = await response.json()

    if (response.ok) {
      navigate({ to: '/posts/$postId', params: { postId } })
    }
  }
}
```

> 🧠 如上所示，可通过 `from` 选项指定导航的起始路由。虽然也可以在每次调用 `navigate` 时传递该选项，但建议在钩子调用时传入，以减少潜在错误并简化类型声明！

### `navigate` 选项

`useNavigate` 返回的 `navigate` 函数接受 [`NavigateOptions` 接口](#navigateoptions-interface)。

## `Navigate` 组件

有时你可能需要在组件挂载时立即导航。虽然可以使用 `useNavigate` 配合副作用（如 `useEffect`），但更简单的方式是直接渲染 `Navigate` 组件：

```tsx
function Component() {
  return <Navigate to="/posts/$postId" params={{ postId: 'my-first-post' }} />
}
```

`Navigate` 组件的作用是在组件挂载时立即导航到指定路由，非常适合处理纯客户端的重定向。但注意，它**绝不能**替代服务端处理的重定向逻辑。

## `router.navigate`

`router.navigate` 方法与 `useNavigate` 返回的 `navigate` 函数功能相同，同样接受 [`NavigateOptions` 接口](#navigateoptions-interface)。不同于 `useNavigate` 钩子，它可以在 `router` 实例可用的任何地方调用，因此非常适合在应用全局（包括框架外部）进行命令式导航。

## `useMatchRoute` 与 `<MatchRoute>`

`useMatchRoute` 钩子和 `<MatchRoute>` 组件功能相同，但钩子更灵活。它们都接受标准的导航 `ToOptions` 接口（作为选项或属性），并在路由匹配时返回 `true/false`。此外，`pending` 选项会在路由正在过渡时返回 `true`，这对展示乐观 UI 非常有用：

```tsx
function Component() {
  return (
    <div>
      <Link to="/users">
        Users
        <MatchRoute to="/users" pending>
          <Spinner />
        </MatchRoute>
      </Link>
    </div>
  )
}
```

组件版 `<MatchRoute>` 还支持以函数形式渲染子元素：

```tsx
function Component() {
  return (
    <div>
      <Link to="/users">
        Users
        <MatchRoute to="/users" pending>
          {(match) => {
            return <Spinner show={match} />
          }}
        </MatchRoute>
      </Link>
    </div>
  )
}
```

钩子版 `useMatchRoute` 返回一个可编程调用的函数来检查路由匹配：

```tsx
function Component() {
  const matchRoute = useMatchRoute()

  useEffect(() => {
    if (matchRoute({ to: '/users', pending: true })) {
      console.info('The /users route is matched and pending')
    }
  })

  return (
    <div>
      <Link to="/users">Users</Link>
    </div>
  )
}
```

---

导航相关的知识就这么多！希望你现在对如何在应用中自如导航有了信心。接下来继续前进吧！
