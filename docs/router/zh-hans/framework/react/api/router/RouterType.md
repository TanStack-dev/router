---
source-updated-at: '2025-03-31T22:13:23.000Z'
translation-updated-at: '2025-05-06T17:45:24.173Z'
id: RouterType
title: Router type
---

`Router` 类型用于描述一个路由器实例。

## `Router` 属性和方法

`Router` 实例具有以下属性和方法：

### `.update` 方法

- 类型: `(newOptions: RouterOptions) => void`
- 使用新的选项更新路由器实例。

### `state` 属性

- 类型: [`RouterState`](./RouterStateType.md)
- 路由器的当前状态。

> ⚠️⚠️⚠️ **`router.state` 始终是最新的，但非响应式。如果在组件中使用 `router.state`，当路由器状态变化时组件不会重新渲染。要获取响应式的路由器状态，请使用 [`useRouterState`](./useRouterStateHook.md) 钩子。**

### `.subscribe` 方法

- 类型: `(eventType: TType, fn: ListenerFn<RouterEvents[TType]>) => (event: RouterEvent) => void`
- 订阅 [`RouterEvent`](./RouterEventsType.md)。
- 返回一个可用于取消订阅事件的函数。
- 提供给返回函数的回调会在事件触发时被调用。

### `.matchRoutes` 方法

- 类型: `(pathname: string, locationSearch: Record<string, any>, opts?: { throwOnError?: boolean; }) => RouteMatch[]`
- 根据路由器的路由树匹配路径名和搜索参数，返回路由匹配数组。
- 如果 `opts.throwOnError` 为 `true`，匹配过程中发生的任何错误都会被抛出（同时也会在路由匹配的 `error` 属性中返回）。

### `.cancelMatch` 方法

- 类型: `(matchId: string) => void`
- 通过调用 `match.abortController.abort()` 取消当前挂起的路由匹配。

### `.cancelMatches` 方法

- 类型: `() => void`
- 通过在每个匹配上调用 `match.abortController.abort()` 取消所有当前挂起的路由匹配。

### `.buildLocation` 方法

构建一个新的解析后的位置对象，可用于后续导航到新位置。

- 类型: `(opts: BuildNextOptions) => ParsedLocation`
- 属性
  - `from`
    - 类型: `string`
    - 可选
    - 导航起始路径。如果未提供，将使用当前路径。
  - `to`
    - 类型: `string | number | null`
    - 可选
    - 导航目标路径。如果为 `null`，将使用当前路径。
  - `params`
    - 类型: `true | Updater<unknown>`
    - 可选
    - 如果为 `true`，则使用当前参数。如果提供函数，会传入当前参数并使用其返回值。
  - `search`
    - 类型: `true | Updater<unknown>`
    - 可选
    - 如果为 `true`，则使用当前搜索参数。如果提供函数，会传入当前搜索参数并使用其返回值。
  - `hash`
    - 类型: `true | Updater<string>`
    - 可选
    - 如果为 `true`，则使用当前哈希值。如果提供函数，会传入当前哈希值并使用其返回值。
  - `state`
    - 类型: `true | NonNullableUpdater<ParsedHistoryState, HistoryState>`
    - 可选
    - 如果为 `true`，则使用当前状态。如果提供函数，会传入当前状态并使用其返回值。
  - `mask`
    - 类型: `object`
    - 可选
    - 包含所有相同的 BuildNextOptions，并额外包含 `unmaskOnReload`。
    - `unmaskOnReload`
      - 类型: `boolean`
      - 可选
      - 如果为 `true`，页面重新加载时会移除路由掩码。可通过在 `Navigate` 选项中设置 `unmaskOnReload` 覆盖此行为。

### `.commitLocation` 方法

将新的位置对象提交到浏览器历史记录。

- 类型
  ```tsx
  type commitLocation = (
    location: ParsedLocation & {
      replace?: boolean
      resetScroll?: boolean
      hashScrollIntoView?: boolean | ScrollIntoViewOptions
      ignoreBlocker?: boolean
    },
  ) => Promise<void>
  ```
- 属性
  - `location`
    - 类型: [`ParsedLocation`](./ParsedLocationType.md)
    - 必需
    - 要提交到浏览器历史记录的位置。
  - `replace`
    - 类型: `boolean`
    - 可选
    - 默认为 `false`。
    - 如果为 `true`，将使用 `history.replace` 而非 `history.push` 提交位置。
  - `resetScroll`
    - 类型: `boolean`
    - 可选
    - 默认为 `true`，提交位置后滚动位置会重置为 0,0。
    - 如果为 `false`，提交位置后不会重置滚动位置。
  - `hashScrollIntoView`
    - 类型: `boolean | ScrollIntoViewOptions`
    - 可选
    - 默认为 `true`，提交位置后会将 ID 匹配哈希的元素滚动到视图中。
    - 如果为 `false`，提交位置后不会滚动元素。
    - 如果传入对象，会作为选项传递给 `scrollIntoView` 方法。
    - 参见 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView) 了解 `ScrollIntoViewOptions` 详情。
  - `ignoreBlocker`
    - 类型: `boolean`
    - 可选
    - 默认为 `false`。
    - 如果为 `true`，导航将忽略任何可能阻止它的拦截器。

### `.navigate` 方法

导航到新位置。

- 类型
  ```tsx
  type navigate = (options: NavigateOptions) => Promise<void>
  ```

### `.invalidate` 方法

通过强制重新调用 `beforeLoad` 和 `load` 函数来使路由匹配失效。

- 类型: `(opts?: {filter?: (d: MakeRouteMatchUnion<TRouter>) => boolean, sync?: boolean}) => Promise<void>`
- 当加载数据可能过期时非常有用。例如，如果有一个显示帖子列表的路由，并且加载函数从 API 获取帖子列表，你可能希望在创建新帖子时使该路由的匹配失效，以确保列表始终最新。
- 如果未提供 `filter`，所有匹配都将失效。
- 如果提供 `filter`，只有 `filter` 返回 `true` 的匹配会失效。
- 如果 `sync` 为 true，此函数返回的 Promise 只会在所有加载器完成后才解析。
- 如果通过命令式 `reset` 路由器的 `CatchBoundary` 来重新触发加载器，也可能需要使路由器失效。

### `.clearCache` 方法

移除缓存的路由匹配。

- 类型: `(opts?: {filter?: (d: MakeRouteMatchUnion<TRouter>) => boolean}) => void`
- 如果未提供 `filter`，所有缓存的匹配都将被移除。
- 如果提供 `filter`，只有 `filter` 返回 `true` 的匹配会被移除。

### `.load` 方法

加载所有当前匹配的路由，并在它们全部加载完成且可渲染时解析。

> ⚠️⚠️⚠️ **`router.load()` 会遵守 `route.staleTime`，如果路由匹配仍处于新鲜状态则不会强制重新加载。如果需要强制重新加载路由匹配，请改用 `router.invalidate()`。**

- 类型: `(opts?: {sync?: boolean}) => Promise<void>`
- 如果 `sync` 为 true，此函数返回的 Promise 只会在所有加载器完成后才解析。
- 此方法最常见的用例是在 SSR 时调用，以确保在尝试将应用流式传输或渲染到客户端之前，当前路由的所有关键数据都已加载。

### `.preloadRoute` 方法

预加载所有匹配提供的 `NavigateOptions` 的路由匹配。

> ⚠️⚠️⚠️ **预加载的路由匹配不会长期存储在路由器状态中，它们仅保留到下一次导航尝试之前。**

- 类型: `(opts?: NavigateOptions) => Promise<RouteMatch[]>`
- 属性
  - `opts`
    - 类型: `NavigateOptions`
    - 可选，默认为当前位置。
    - 用于确定要预加载哪些路由匹配的选项。
- 返回
  - 一个 Promise，解析为所有预加载的路由匹配数组。

### `.loadRouteChunk` 方法

加载路由的 JS 代码块。

- 类型: `(route: AnyRoute) => Promise<void>`

### `.matchRoute` 方法

根据路由树匹配路径名和搜索参数，返回路由匹配的参数，如果未找到匹配则返回 false。

- 类型: `(dest: ToOptions, matchOpts?: MatchRouteOptions) => RouteMatch['params'] | false`
- 属性
  - `dest`
    - 类型: `ToOptions`
    - 必需
    - 要匹配的目标。
  - `matchOpts`
    - 类型: `MatchRouteOptions`
    - 可选
    - 用于匹配目标的选项。
- 返回
  - 如果找到匹配，返回路由匹配的参数。
  - 如果未找到匹配，返回 `false`。

### `.dehydrate` 方法

将路由器的关键状态脱水为可序列化对象，可在初始请求中发送到客户端。

- 类型: `() => DehydratedRouter`
- 返回
  - 包含路由器关键状态的可序列化对象。

### `.hydrate` 方法

从服务器在初始请求中发送的可序列化对象中恢复路由器的关键状态。

- 类型: `(dehydrated: DehydratedRouter) => void`
- 属性
  - `dehydrated`
    - 类型: `DehydratedRouter`
    - 必需
    - 服务器发送的脱水后的路由器状态。
