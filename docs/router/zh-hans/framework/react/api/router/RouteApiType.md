---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:52:09.833Z'
id: RouteApiType
title: RouteApi Type
---

`RouteApi` 描述了一个实例，该实例提供了类型安全版本的常用钩子，如 `useParams`、`useSearch`、`useRouteContext`、`useNavigate`、`useLoaderData` 和 `useLoaderDeps`，这些钩子已预先绑定到特定路由 ID 及其对应的注册路由类型。

## `RouteApi` 属性和方法

`RouteApi` 具有以下属性和方法：

### `useMatch` 方法

```tsx
  useMatch<TSelected = TAllContext>(opts?: {
    select?: (match: TAllContext) => TSelected
  }): TSelected
```

- 这是 [`useMatch`](./useMatchHook.md) 钩子的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
- 选项
  - `opts.select`
    - 可选
    - `(match: RouteMatch) => TSelected`
    - 如果提供，此函数将接收路由匹配对象作为参数，其返回值将作为 `useMatch` 的返回结果。该值还将用于通过浅层相等性检查决定是否重新渲染父组件。
  - `opts.structuralSharing`
    - 可选
    - `boolean`
    - 配置是否为 `select` 返回的值启用结构共享。
    - 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函数，则返回该函数的返回值。
  - 如果未提供 `select` 函数，则返回 `RouteMatch` 对象；若 `opts.strict` 为 `false`，则返回宽松版本的 `RouteMatch` 对象。

### `useRouteContext` 方法

```tsx
  useRouteContext<TSelected = TAllContext>(opts?: {
    select?: (search: TAllContext) => TSelected
  }): TSelected
```

- 这是 [`useRouteContext`](./useRouteContextHook.md) 钩子的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
- 选项
  - `opts.select`
    - 可选
    - `(match: RouteContext) => TSelected`
    - 如果提供，此函数将接收路由上下文对象作为参数，其返回值将作为 `useRouteContext` 的返回结果。该值还将用于通过浅层相等性检查决定是否重新渲染父组件。
- 返回值
  - 如果提供了 `select` 函数，则返回该函数的返回值。
  - 如果未提供 `select` 函数，则返回 `RouteContext` 对象；若 `opts.strict` 为 `false`，则返回宽松版本的 `RouteContext` 对象。

### `useSearch` 方法

```tsx
  useSearch<TSelected = TFullSearchSchema>(opts?: {
    select?: (search: TFullSearchSchema) => TSelected
  }): TSelected
```

- 这是 [`useSearch`](./useSearchHook.md) 钩子的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
- 选项
  - `opts.select`
    - 可选
    - `(match: TFullSearchSchema) => TSelected`
    - 如果提供，此函数将接收搜索参数对象作为参数，其返回值将作为 `useSearch` 的返回结果。该值还将用于通过浅层相等性检查决定是否重新渲染父组件。
  - `opts.structuralSharing`
    - 可选
    - `boolean`
    - 配置是否为 `select` 返回的值启用结构共享。
    - 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函数，则返回该函数的返回值。
  - 如果未提供 `select` 函数，则返回 `TFullSearchSchema` 对象；若 `opts.strict` 为 `false`，则返回宽松版本的 `TFullSearchSchema` 对象。

### `useParams` 方法

```tsx
  useParams<TSelected = TAllParams>(opts?: {
    select?: (params: TAllParams) => TSelected
  }): TSelected
```

- 这是 [`useParams`](./useParamsHook.md) 钩子的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
- 选项
  - `opts.select`
    - 可选
    - `(match: TAllParams) => TSelected`
    - 如果提供，此函数将接收路由参数对象作为参数，其返回值将作为 `useParams` 的返回结果。该值还将用于通过浅层相等性检查决定是否重新渲染父组件。
  - `opts.structuralSharing`
    - 可选
    - `boolean`
    - 配置是否为 `select` 返回的值启用结构共享。
    - 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函数，则返回该函数的返回值。
  - 如果未提供 `select` 函数，则返回 `TAllParams` 对象；若 `opts.strict` 为 `false`，则返回宽松版本的 `TAllParams` 对象。

### `useLoaderData` 方法

```tsx
  useLoaderData<TSelected = TLoaderData>(opts?: {
    select?: (search: TLoaderData) => TSelected
  }): TSelected
```

- 这是 [`useLoaderData`](./useLoaderDataHook.md) 钩子的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
- 选项
  - `opts.select`
    - 可选
    - `(match: TLoaderData) => TSelected`
    - 如果提供，此函数将接收加载器数据对象作为参数，其返回值将作为 `useLoaderData` 的返回结果。该值还将用于通过浅层相等性检查决定是否重新渲染父组件。
  - `opts.structuralSharing`
    - 可选
    - `boolean`
    - 配置是否为 `select` 返回的值启用结构共享。
    - 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函数，则返回该函数的返回值。
  - 如果未提供 `select` 函数，则返回 `TLoaderData` 对象；若 `opts.strict` 为 `false`，则返回宽松版本的 `TLoaderData` 对象。

### `useLoaderDeps` 方法

```tsx
  useLoaderDeps<TSelected = TLoaderDeps>(opts?: {
    select?: (search: TLoaderDeps) => TSelected
  }): TSelected
```

- 这是 [`useLoaderDeps`](./useLoaderDepsHook.md) 钩子的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
- 选项
  - `opts.select`
    - 可选
    - `(match: TLoaderDeps) => TSelected`
    - 如果提供，此函数将接收加载器依赖对象作为参数，其返回值将作为 `useLoaderDeps` 的返回结果。
  - `opts.structuralSharing`
    - 可选
    - `boolean`
    - 配置是否为 `select` 返回的值启用结构共享。
    - 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函数，则返回该函数的返回值。
  - 如果未提供 `select` 函数，则返回 `TLoaderDeps` 对象。

### `useNavigate` 方法

```tsx
  useNavigate(): // navigate function
```

- 这是 [`useNavigate`](./useNavigateHook.md) 的类型安全版本，已预先绑定到创建 `RouteApi` 实例时使用的路由 ID。
