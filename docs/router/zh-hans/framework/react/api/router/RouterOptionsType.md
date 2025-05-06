---
source-updated-at: '2025-02-28T11:38:02.000Z'
translation-updated-at: '2025-05-06T17:47:29.905Z'
id: RouterOptions
title: RouterOptions
---

`RouterOptions` 类型包含了用于配置路由实例的所有选项。

## RouterOptions 属性

`RouterOptions` 类型接受一个包含以下属性和方法的对象：

### `routeTree` 属性

- 类型: `AnyRoute`
- 必填
- 用于配置路由实例的路由树。

### `history` 属性

- 类型: `RouterHistory`
- 可选
- 用于管理浏览器历史记录的历史对象。如果未提供，将创建并使用一个新的 `createBrowserHistory` 实例。

### `stringifySearch` 方法

- 类型: `(search: Record<string, any>) => string`
- 可选
- 用于在生成链接时将搜索参数序列化为字符串的函数。
- 默认为 `defaultStringifySearch`。

### `parseSearch` 方法

- 类型: `(search: string) => Record<string, any>`
- 可选
- 用于在解析当前位置时解析搜索参数的函数。
- 默认为 `defaultParseSearch`。

### `search.strict` 属性

- 类型: `boolean`
- 可选
- 默认为 `false`
- 配置如何处理未知的搜索参数（即未被任何 `validateSearch` 返回的参数）。
- 如果为 `false`，将保留未知的搜索参数。
- 如果为 `true`，将移除未知的搜索参数。

### `defaultPreload` 属性

- 类型: `undefined | false | 'intent' | 'viewport' | 'render'`
- 可选
- 默认为 `false`
- 如果为 `false`，默认情况下不会以任何方式预加载路由。
- 如果为 `'intent'`，默认情况下会在用户悬停在链接上或检测到 `<Link>` 上的 `touchstart` 事件时预加载路由。
- 如果为 `'viewport'`，默认情况下会在路由位于浏览器视口内时预加载。
- 如果为 `'render'`，默认情况下会在路由渲染到 DOM 中时立即预加载。

### `defaultPreloadDelay` 属性

- 类型: `number`
- 可选
- 默认为 `50`
- 路由在悬停或触摸后必须延迟的毫秒数，然后才会被预加载。

### `defaultComponent` 属性

- 类型: `RouteComponent`
- 可选
- 默认为 `Outlet`
- 如果未提供组件，路由应使用的默认 `component`。

### `defaultErrorComponent` 属性

- 类型: `RouteComponent`
- 可选
- 默认为 `ErrorComponent`
- 如果未提供错误组件，路由应使用的默认 `errorComponent`。

### `defaultNotFoundComponent` 属性

- 类型: `NotFoundRouteComponent`
- 可选
- 默认为 `NotFound`
- 如果未提供 notFound 组件，路由应使用的默认 `notFoundComponent`。

### `defaultPendingComponent` 属性

- 类型: `RouteComponent`
- 可选
- 如果未提供 pending 组件，路由应使用的默认 `pendingComponent`。

### `defaultPendingMs` 属性

- 类型: `number`
- 可选
- 默认为 `1000`
- 如果未提供 pendingMs，路由应使用的默认 `pendingMs`。

### `defaultPendingMinMs` 属性

- 类型: `number`
- 可选
- 默认为 `500`
- 如果未提供 pendingMinMs，路由应使用的默认 `pendingMinMs`。

### `defaultStaleTime` 属性

- 类型: `number`
- 可选
- 默认为 `0`
- 如果未提供 staleTime，路由应使用的默认 `staleTime`。

### `defaultPreloadStaleTime` 属性

- 类型: `number`
- 可选
- 默认为 `30_000` 毫秒（30 秒）
- 如果未提供 preloadStaleTime，路由应使用的默认 `preloadStaleTime`。

### `defaultPreloadGcTime` 属性

- 类型: `number`
- 可选
- 默认为 `routerOptions.defaultGcTime`，默认为 30 分钟。
- 如果未提供 preloadGcTime，路由应使用的默认 `preloadGcTime`。

### `defaultGcTime` 属性

- 类型: `number`
- 可选
- 默认为 30 分钟。
- 如果未提供 gcTime，路由应使用的默认 `gcTime`。

### `defaultOnCatch` 属性

- 类型: `(error: Error, errorInfo: ErrorInfo) => void`
- 可选
- 用于处理 Router ErrorBoundary 捕获的错误的默认 `onCatch` 处理器。

### `defaultViewTransition` 属性

- 类型: `boolean | ViewTransitionOptions`
- 可选
- 如果为 `true`，路由导航将使用 `document.startViewTransition()` 调用。
- 如果为 [`ViewTransitionOptions`](./ViewTransitionOptionsType.md)，路由导航将使用 `document.startViewTransition({update, types})` 调用，其中 `types` 将是传递的 `ViewTransitionOptions["types"]` 字符串数组。如果浏览器不支持 viewTransition 类型，导航将回退到普通的 `document.startTransition()`，与传递 `true` 时相同。
- 如果浏览器不支持此 API，此选项将被忽略。
- 有关此函数工作原理的更多信息，请参阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/startViewTransition)。
- 有关 viewTransition 类型的更多信息，请参阅 [Google](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)。

### `defaultHashScrollIntoView` 属性

- 类型: `boolean | ScrollIntoViewOptions`
- 可选
- 默认为 `true`，因此在位置提交到历史记录后，将滚动到与哈希匹配的元素。
- 如果为 `false`，位置提交到历史记录后，不会滚动到与哈希匹配的元素。
- 如果提供对象，它将作为选项传递给 `scrollIntoView` 方法。
- 有关 `ScrollIntoViewOptions` 的更多信息，请参阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)。

### `caseSensitive` 属性

- 类型: `boolean`
- 可选
- 默认为 `false`
- 如果为 `true`，所有路由将区分大小写匹配。

### `basepath` 属性

- 类型: `string`
- 可选
- 默认为 `/`
- 整个路由器的基本路径。这对于将路由实例挂载到子路径很有用。

### `context` 属性

- 类型: `any`
- 可选或必填（如果根路由是使用 [`createRootRouteWithContext()`](./createRootRouteWithContextFunction.md) 创建的）。
- 将提供给路由树中所有路由的根上下文。这可用于为树中的所有路由提供上下文，而无需单独为每个路由提供。

### `dehydrate` 方法

- 类型: `() => TDehydrated`
- 可选
- 当路由器脱水时将调用的函数。此函数的返回值将被序列化并存储在路由器的脱水状态中。

### `hydrate` 方法

- 类型: `(dehydrated: TDehydrated) => void`
- 可选
- 当路由器水合时将调用的函数。此函数的返回值将被序列化并存储在路由器的脱水状态中。

### `routeMasks` 属性

- 类型: `RouteMask[]`
- 可选
- 用于屏蔽路由树中路由的路由掩码数组。路由屏蔽是指以不同于配置匹配路径的路径显示路由，例如模态弹出窗口，在共享时将解除屏蔽以显示模态内容而非模态上下文。

### `unmaskOnReload` 属性

- 类型: `boolean`
- 可选
- 默认为 `false`
- 如果为 `true`，默认情况下，页面重新加载时将移除路由掩码。可以通过在掩码上设置 `unmaskOnReload` 选项或在 `Navigate` 选项中设置 `unmaskOnReload` 选项来按掩码或按导航覆盖此行为。

### `Wrap` 属性

- 类型: `React.Component`
- 可选
- 用于包装整个路由器的组件。这对于为整个路由器提供上下文很有用。仅应使用非 DOM 渲染组件（如 providers），其他任何内容都会导致水合错误。

**示例**

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  Wrap: ({ children }) => {
    return <MyContext.Provider value={myContext}>{children}</MyContext>
  },
})
```

### `InnerWrap` 属性

- 类型: `React.Component`
- 可选
- 用于包装路由器内部内容的组件。这对于为需要访问路由器上下文和钩子的路由器内部内容提供上下文很有用。仅应使用非 DOM 渲染组件（如 providers），其他任何内容都会导致水合错误。

**示例**

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  InnerWrap: ({ children }) => {
    const routerState = useRouterState()

    return (
      <MyContext.Provider value={myContext}>
        {children}
      </MyContext>
    )
  },
})
```

### `notFoundMode` 属性

- 类型: `'root' | 'fuzzy'`
- 可选
- 默认为 `'fuzzy'`
- 此属性控制 TanStack Router 在无法找到匹配当前位置的路由时的处理方式。有关更多信息，请参阅 [未找到错误指南](../../guide/not-found-errors.md)。

### `notFoundRoute` 属性

- **已弃用**
- 类型: `NotFoundRoute`
- 可选
- 将用作路由树每个分支的默认未找到路由的路由。可以通过为分支的根路由的 `NotFoundRoute` 选项提供未找到路由来按分支覆盖此行为。

### `errorSerializer` 属性

- 类型: [`RouterErrorSerializer`]
- 可选
- 用于确定错误在服务器和客户端之间如何序列化和反序列化的序列化器对象。

#### `errorSerializer.serialize` 方法

- 类型: `(err: unknown) => TSerializedError`
- 此方法用于定义错误在存储到路由器的脱水状态时如何序列化。

#### `errorSerializer.deserialize` 方法

- 类型: `(err: TSerializedError) => unknown`
- 此方法用于定义错误从路由器的脱水状态中如何反序列化。

### `trailingSlash` 属性

- 类型: `'always' | 'never' | 'preserve'`
- 可选
- 默认为 `never`
- 配置如何处理尾部斜杠。`'always'` 会在不存在时添加尾部斜杠，`'never'` 会在存在时移除尾部斜杠，`'preserve'` 不会修改尾部斜杠。

### `pathParamsAllowedCharacters` 属性

- 类型: `Array<';' | ':' | '@' | '&' | '=' | '+' | '$' | ','>`
- 可选
- 配置路径参数中允许哪些通常会被 `encodeURIComponent` 转义的 URI 字符。

### `defaultStructuralSharing` 属性

- 类型: `boolean`
- 可选
- 默认为 `false`
- 配置是否默认启用细粒度选择器的结构共享。
- 有关更多信息，请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

### `defaultRemountDeps` 属性

- 类型:

```tsx
type defaultRemountDeps = (opts: RemountDepsOptions) => any

interface RemountDepsOptions<
  in out TRouteId,
  in out TFullSearchSchema,
  in out TAllParams,
  in out TLoaderDeps,
> {
  routeId: TRouteId
  search: TFullSearchSchema
  params: TAllParams
  loaderDeps: TLoaderDeps
}
```

- 可选
- 默认函数，用于确定导航后是否应重新挂载路由组件。如果此函数返回的值与之前不同，则会重新挂载。
- 返回值需要可 JSON 序列化。
- 默认情况下，如果路由组件在导航后保持活动状态，则不会重新挂载。

示例：  
如果要配置在 `params` 更改时重新挂载所有路由组件，请使用：

```tsx
remountDeps: ({ params }) => params
```
