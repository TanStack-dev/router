---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:54.438Z'
id: RouteApiType
title: RouteApi Type
---

`RouteApi` 描述了一個實例，該實例提供了類型安全 (type-safe) 版本的常用鉤子 (hooks)，例如 `useParams`、`useSearch`、`useRouteContext`、`useNavigate`、`useLoaderData` 和 `useLoaderDeps`，這些鉤子已預先綁定到特定的路由 ID 和對應的已註冊路由類型。

## `RouteApi` 屬性和方法

`RouteApi` 具有以下屬性和方法：

### `useMatch` 方法

```tsx
  useMatch<TSelected = TAllContext>(opts?: {
    select?: (match: TAllContext) => TSelected
  }): TSelected
```

- 這是 [`useMatch`](./useMatchHook.md) 鉤子的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
- 選項
  - `opts.select`
    - 可選
    - `(match: RouteMatch) => TSelected`
    - 如果提供，此函數將以路由匹配 (route match) 為參數被調用，其返回值將從 `useMatch` 返回。此值還將用於通過淺層相等性檢查 (shallow equality checks) 來判斷鉤子是否應重新渲染其父組件。
  - `opts.structuralSharing`
    - 可選
    - `boolean`
    - 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
    - 詳見 [渲染優化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函數，則返回 `select` 函數的返回值。
  - 如果未提供 `select` 函數，則返回 `RouteMatch` 物件；如果 `opts.strict` 為 `false`，則返回 `RouteMatch` 物件的寬鬆版本。

### `useRouteContext` 方法

```tsx
  useRouteContext<TSelected = TAllContext>(opts?: {
    select?: (search: TAllContext) => TSelected
  }): TSelected
```

- 這是 [`useRouteContext`](./useRouteContextHook.md) 鉤子的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
- 選項
  - `opts.select`
    - 可選
    - `(match: RouteContext) => TSelected`
    - 如果提供，此函數將以路由匹配 (route match) 為參數被調用，其返回值將從 `useRouteContext` 返回。此值還將用於通過淺層相等性檢查 (shallow equality checks) 來判斷鉤子是否應重新渲染其父組件。
- 返回值
  - 如果提供了 `select` 函數，則返回 `select` 函數的返回值。
  - 如果未提供 `select` 函數，則返回 `RouteContext` 物件；如果 `opts.strict` 為 `false`，則返回 `RouteContext` 物件的寬鬆版本。

### `useSearch` 方法

```tsx
  useSearch<TSelected = TFullSearchSchema>(opts?: {
    select?: (search: TFullSearchSchema) => TSelected
  }): TSelected
```

- 這是 [`useSearch`](./useSearchHook.md) 鉤子的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
- 選項
  - `opts.select`
    - 可選
    - `(match: TFullSearchSchema) => TSelected`
    - 如果提供，此函數將以路由匹配 (route match) 為參數被調用，其返回值將從 `useSearch` 返回。此值還將用於通過淺層相等性檢查 (shallow equality checks) 來判斷鉤子是否應重新渲染其父組件。
  - `opts.structuralSharing`
    - 可選
    - `boolean`
    - 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
    - 詳見 [渲染優化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函數，則返回 `select` 函數的返回值。
  - 如果未提供 `select` 函數，則返回 `TFullSearchSchema` 物件；如果 `opts.strict` 為 `false`，則返回 `TFullSearchSchema` 物件的寬鬆版本。

### `useParams` 方法

```tsx
  useParams<TSelected = TAllParams>(opts?: {
    select?: (params: TAllParams) => TSelected
  }): TSelected
```

- 這是 [`useParams`](./useParamsHook.md) 鉤子的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
- 選項
  - `opts.select`
    - 可選
    - `(match: TAllParams) => TSelected`
    - 如果提供，此函數將以路由匹配 (route match) 為參數被調用，其返回值將從 `useParams` 返回。此值還將用於通過淺層相等性檢查 (shallow equality checks) 來判斷鉤子是否應重新渲染其父組件。
  - `opts.structuralSharing`
    - 可選
    - `boolean`
    - 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
    - 詳見 [渲染優化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函數，則返回 `select` 函數的返回值。
  - 如果未提供 `select` 函數，則返回 `TAllParams` 物件；如果 `opts.strict` 為 `false`，則返回 `TAllParams` 物件的寬鬆版本。

### `useLoaderData` 方法

```tsx
  useLoaderData<TSelected = TLoaderData>(opts?: {
    select?: (search: TLoaderData) => TSelected
  }): TSelected
```

- 這是 [`useLoaderData`](./useLoaderDataHook.md) 鉤子的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
- 選項
  - `opts.select`
    - 可選
    - `(match: TLoaderData) => TSelected`
    - 如果提供，此函數將以路由匹配 (route match) 為參數被調用，其返回值將從 `useLoaderData` 返回。此值還將用於通過淺層相等性檢查 (shallow equality checks) 來判斷鉤子是否應重新渲染其父組件。
  - `opts.structuralSharing`
    - 可選
    - `boolean`
    - 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
    - 詳見 [渲染優化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函數，則返回 `select` 函數的返回值。
  - 如果未提供 `select` 函數，則返回 `TLoaderData` 物件；如果 `opts.strict` 為 `false`，則返回 `TLoaderData` 物件的寬鬆版本。

### `useLoaderDeps` 方法

```tsx
  useLoaderDeps<TSelected = TLoaderDeps>(opts?: {
    select?: (search: TLoaderDeps) => TSelected
  }): TSelected
```

- 這是 [`useLoaderDeps`](./useLoaderDepsHook.md) 鉤子的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
- 選項
  - `opts.select`
    - 可選
    - `(match: TLoaderDeps) => TSelected`
    - 如果提供，此函數將以路由匹配 (route match) 為參數被調用，其返回值將從 `useLoaderDeps` 返回。
  - `opts.structuralSharing`
    - 可選
    - `boolean`
    - 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
    - 詳見 [渲染優化指南](../../guide/render-optimizations.md)。
- 返回值
  - 如果提供了 `select` 函數，則返回 `select` 函數的返回值。
  - 如果未提供 `select` 函數，則返回 `TLoaderDeps` 物件。

### `useNavigate` 方法

```tsx
  useNavigate(): // navigate function
```

- 這是 [`useNavigate`](./useNavigateHook.md) 的類型安全 (type-safe) 版本，已預先綁定到創建 `RouteApi` 實例時使用的路由 ID。
