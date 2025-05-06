---
source-updated-at: '2025-02-28T11:38:02.000Z'
translation-updated-at: '2025-05-06T17:50:28.662Z'
id: RouteOptionsType
title: RouteOptions type
---

`RouteOptions` 类型用于描述创建路由时可用的配置选项。

## RouteOptions 属性

`RouteOptions` 类型接收包含以下属性的对象：

### `getParentRoute` 方法

- 类型: `() => TParentRoute`
- 必需
- 返回当前创建路由的父路由的函数。此方法用于为子路由配置提供完整的类型安全，并确保路由树正确构建。

### `path` 属性

- 类型: `string`
- 必需（除非提供 `id` 将路由配置为无路径布局路由）
- 用于匹配路由的路径片段。

### `id` 属性

- 类型: `string`
- 可选（但如果未提供 `path` 则为必需）
- 如果要将路由配置为无路径布局路由，则需提供此唯一标识符。若提供此属性，路由将不会匹配位置路径名，其子路由会被扁平化到父路由中进行匹配。

### `component` 属性

- 类型: `RouteComponent` 或 `LazyRouteComponent`
- 可选 - 默认为 `<Outlet />`
- 路由匹配时渲染的内容。

### `errorComponent` 属性

- 类型: `RouteComponent` 或 `LazyRouteComponent`
- 可选 - 默认为 `routerOptions.defaultErrorComponent`
- 路由发生错误时渲染的内容。

### `pendingComponent` 属性

- 类型: `RouteComponent` 或 `LazyRouteComponent`
- 可选 - 默认为 `routerOptions.defaultPendingComponent`
- 当路由处于挂起状态且达到 `pendingMs` 阈值时渲染的内容。

### `notFoundComponent` 属性

- 类型: `NotFoundRouteComponent` 或 `LazyRouteComponent`
- 可选 - 默认为 `routerOptions.defaultNotFoundComponent`
- 路由未找到时渲染的内容。

### `validateSearch` 方法

- 类型: `(rawSearchParams: unknown) => TSearchSchema`
- 可选
- 此函数会在路由匹配时调用，接收当前位置的原始搜索参数并返回解析后的有效搜索参数。如果此函数抛出错误，路由将进入错误状态，并在渲染时抛出该错误。如果未抛出错误，其返回值将用作路由的搜索参数，且返回类型会被推断到路由器的其余部分。
- 可选地，参数类型可以用 `SearchSchemaInput` 类型标记，如：`(searchParams: TSearchSchemaInput & SearchSchemaInput) => TSearchSchema`。如果存在此标记，`TSearchSchemaInput` 将用于为 `<Link />` 和 `navigate()` 的 `search` 属性提供类型（而非 `TSearchSchema`）。`TSearchSchemaInput` 与 `TSearchSchema` 的区别可用于表达可选搜索参数等场景。

### `search.middlewares` 属性

- 类型: `(({search: TSearchSchema, next: (newSearch: TSearchSchema) => TSearchSchema}) => TSearchSchema)[]`
- 可选
- 搜索中间件是在为路由或其子路由生成新链接时转换搜索参数的函数。
- 搜索中间件接收当前搜索参数（如果是第一个运行的中间件）或由前一个中间件调用 `next` 触发。

### `parseParams` 方法 (⚠️ 已弃用)

- 类型: `(rawParams: Record<string, string>) => TParams`
- 可选
- 此函数会在路由匹配时调用，接收当前位置的原始参数并返回解析后的有效参数。如果此函数抛出错误，路由将进入错误状态，并在渲染时抛出该错误。如果未抛出错误，其返回值将用作路由的参数，且返回类型会被推断到路由器的其余部分。

### `stringifyParams` 方法 (⚠️ 已弃用)

- 类型: `(params: TParams) => Record<string, string>`
- 如果提供了 `parseParams` 则为必需
- 此函数会在使用路由的解析参数构建位置时调用，应返回有效的 `Record<string, string>` 映射对象。

### `params.parse` 方法

- 类型: `(rawParams: Record<string, string>) => TParams`
- 可选
- 此函数会在路由匹配时调用，接收当前位置的原始参数并返回解析后的有效参数。如果此函数抛出错误，路由将进入错误状态，并在渲染时抛出该错误。如果未抛出错误，其返回值将用作路由的参数，且返回类型会被推断到路由器的其余部分。

### `params.stringify` 方法

- 类型: `(params: TParams) => Record<string, string>`
- 此函数会在使用路由的解析参数构建位置时调用，应返回有效的 `Record<string, string>` 映射对象。

### `beforeLoad` 方法

- 类型:

```tsx
type beforeLoad = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TParentContext
    location: ParsedLocation
    navigate: NavigateFn<AnyRoute> // @deprecated
    buildLocation: BuildLocationFn<AnyRoute>
    cause: 'enter' | 'stay'
  },
) => Promise<TRouteContext> | TRouteContext | void
```

- 可选
- [`ParsedLocation`](./ParsedLocationType.md)
- 此异步函数在路由加载前调用。如果在此抛出错误，路由的加载器不会被调用且路由不会渲染。如果在导航期间抛出错误，导航会被取消且错误会传递给 `onError` 函数。如果在预加载事件期间抛出错误，错误会记录到控制台且预加载会失败。
- 如果此函数返回 Promise，路由将进入挂起状态并导致渲染暂停直到 Promise 解析。如果达到此路由的 `pendingMs` 阈值，`pendingComponent` 会显示直到解析完成。如果 Promise 拒绝，路由将进入错误状态并在渲染时抛出错误。
- 如果此函数返回 `TRouteContext` 对象，该对象会合并到路由的上下文中，并可在 `loader` 和其他相关路由组件/方法中访问。
- 常用场景是通过此函数检查用户是否已认证，如果未认证则重定向到登录页。为此，可以从此函数返回或抛出 `redirect` 对象。

> 🚧 `opts.navigate` 已弃用，将在下一个主要版本中移除。请改用 `throw redirect({ to: '/somewhere' })`。更多关于 `redirect` 函数的信息请参阅[此处](./redirectFunction.md)。

### `loader` 方法

- 类型:

```tsx
type loader = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TAllContext
    location: ParsedLocation
    navigate: NavigateFn<AnyRoute> // @deprecated
    buildLocation: BuildLocationFn<AnyRoute>
    cause: 'enter' | 'stay'
  },
) => Promise<TLoaderData> | TLoaderData | void
```

- 可选
- [`ParsedLocation`](./ParsedLocationType.md)
- 此异步函数在路由匹配时调用，并接收路由的匹配对象。如果在此抛出错误，路由将进入错误状态并在渲染时抛出错误。如果在导航期间抛出错误，导航会被取消且错误会传递给 `onError` 函数。如果在预加载事件期间抛出错误，错误会记录到控制台且预加载会失败。
- 如果此函数返回 Promise，路由将进入挂起状态并导致渲染暂停直到 Promise 解析。如果达到此路由的 `pendingMs` 阈值，`pendingComponent` 会显示直到解析完成。如果 Promise 拒绝，路由将进入错误状态并在渲染时抛出错误。
- 如果此函数返回 `TLoaderData` 对象，该对象会存储在路由匹配上直到匹配不再活跃。在下一个 `<Outlet />` 渲染前，可通过 `useLoaderData` 钩子在路由匹配的子组件中访问此数据。

> 🚧 `opts.navigate` 已弃用，将在下一个主要版本中移除。请改用 `throw redirect({ to: '/somewhere' })`。更多关于 `redirect` 函数的信息请参阅[此处](./redirectFunction.md)。

### `loaderDeps` 方法

- 类型:

```tsx
type loaderDeps = (opts: { search: TFullSearchSchema }) => Record<string, any>
```

- 可选
- 此函数会在路由匹配前调用，用于为路由匹配提供额外的唯一标识，并作为匹配重新加载的依赖跟踪器。应返回可序列化的值，用于在导航间唯一标识路由匹配。
- 默认情况下，路径参数已用于唯一标识路由匹配，因此无需在此返回这些参数。
- 如果路由匹配依赖搜索参数进行唯一标识，则需在此返回这些参数，以便在 `loader` 的 `deps` 参数中可用。

### `staleTime` 属性

- 类型: `number`
- 可选
- 默认为 `routerOptions.defaultStaleTime`（默认为 `0`）
- 路由匹配的加载器数据被视为新鲜的时间（毫秒）。如果在此时间范围内再次匹配路由，其加载器数据不会重新加载。

### `preloadStaleTime` 属性

- 类型: `number`
- 可选
- 默认为 `routerOptions.defaultPreloadStaleTime`（默认为 `30_000` 毫秒，即 30 秒）
- 预加载时路由匹配的加载器数据被视为新鲜的时间（毫秒）。如果在此时间范围内再次预加载路由匹配，其加载器数据不会重新加载。如果在此时间范围内加载路由匹配（用于导航），则使用正常的 `staleTime`。

### `gcTime` 属性

- 类型: `number`
- 可选
- 默认为 `routerOptions.defaultGcTime`（默认为 30 分钟）。
- 路由匹配的加载器数据在预加载后或不再使用时保留在内存中的时间（毫秒）。

### `shouldReload` 属性

- 类型: `boolean | ((args: LoaderArgs) => boolean)`
- 可选
- 如果为 `false` 或返回 `false`，路由匹配的加载器数据在后续匹配时不会重新加载。
- 如果为 `true` 或返回 `true`，路由匹配的加载器数据在后续匹配时会重新加载。
- 如果为 `undefined` 或返回 `undefined`，路由匹配的加载器数据将遵循默认的“陈旧但重新验证”行为。

### `caseSensitive` 属性

- 类型: `boolean`
- 可选
- 如果为 `true`，此路由将区分大小写进行匹配。

### `wrapInSuspense` 属性

- 类型: `boolean`
- 可选
- 如果为 `true`，此路由将强制包裹在 Suspense 边界中，无论从其提供的组件中是否找到需要这样做的原因。

### `pendingMs` 属性

- 类型: `number`
- 可选
- 默认为 `routerOptions.defaultPendingMs`（默认为 `1000`）
- 路由必须挂起的阈值时间（毫秒），超过此时间后显示 `pendingComponent`。

### `pendingMinMs` 属性

- 类型: `number`
- 可选
- 默认为 `routerOptions.defaultPendingMinMs`（默认为 `500`）
- 如果显示挂起组件，其最少显示时间（毫秒）。用于防止挂起组件在屏幕上闪现。

### `preloadMaxAge` 属性

- 类型: `number`
- 可选
- 默认为 `30_000` 毫秒（30 秒）
- 路由预加载数据的最大缓存时间（毫秒）。如果在此时间范围内未匹配路由，其加载器数据将被丢弃。

### `preSearchFilters` 属性 (⚠️ 已弃用，请使用 `search.middlewares`)

- 类型: `((search: TFullSearchSchema) => TFullSearchSchema)[]`
- 可选
- 生成到此路由或其子路由的新链接时调用的函数数组。
- 每个函数接收当前搜索参数并应返回新的搜索参数对象用于生成链接。
- 前缀为 `pre` 是因为其在用户传递给 `navigate`/`Link` 等的函数修改搜索参数之前调用。

### `postSearchFilters` 属性 (⚠️ 已弃用，请使用 `search.middlewares`)

- 类型: `((search: TFullSearchSchema) => TFullSearchSchema)[]`
- 可选
- 生成到此路由或其子路由的新链接时调用的函数数组。
- 每个函数接收当前搜索参数并应返回新的搜索参数对象用于生成链接。
- 前缀为 `post` 是因为其在用户传递给 `navigate`/`Link` 等的函数修改搜索参数之后调用。

### `onError` 属性

- 类型: `(error: any) => void`
- 可选
- 在导航或预加载事件期间抛出错误时调用的函数。
- 如果此函数抛出 [`redirect`](./redirectFunction.md)，路由器会立即处理并应用此重定向。

### `onEnter` 属性

- 类型: `(match: RouteMatch) => void`
- 可选
- 当路由在先前位置未匹配但在当前位置匹配并加载后调用的函数。

### `onStay` 属性

- 类型: `(match: RouteMatch) => void`
- 可选
- 当路由在先前位置已匹配且在当前位置再次匹配并加载后调用的函数。

### `onLeave` 属性

- 类型: `(match: RouteMatch) => void`
- 可选
- 当路由在先前位置已匹配但在当前位置不再匹配后调用的函数。

### `onCatch` 属性

- 类型: `(error: Error, errorInfo: ErrorInfo) => void`
- 可选 - 默认为 `routerOptions.defaultOnCatch`
- 当路由捕获到错误时调用的函数。

### `remountDeps` 方法

- 类型:

```tsx
type remountDeps = (opts: RemountDepsOptions) => any

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
- 此函数用于确定路由组件是否应在导航后重新挂载。如果此函数返回的值与之前不同，则会重新挂载。
- 返回值需为可 JSON 序列化的值。
- 默认情况下，如果路由组件在导航后仍保持活跃，则不会重新挂载。

示例：  
如果希望在 `params` 变化时重新挂载路由组件，可使用：

```tsx
remountDeps: ({ params }) => params
```
