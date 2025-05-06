---
source-updated-at: 2025-01-30T04:47:58.000Z
translation-updated-at: 2025-04-05T03:37:53.000Z
title: 渲染优化
---

TanStack Router 包含多项优化措施，确保组件仅在必要时重新渲染。这些优化包括：

## 结构共享

TanStack Router 采用称为"结构共享"的技术，在重新渲染时尽可能保留引用不变，这对于存储在 URL 中的状态（如搜索参数）特别有用。

例如，考虑一个带有 `foo` 和 `bar` 两个搜索参数的 `details` 路由：

```tsx
const search = Route.useSearch()
```

当仅改变 `bar` 参数，从 `/details?foo=f1&bar=b1` 导航到 `/details?foo=f1&bar=b2` 时，`search.foo` 将保持引用稳定，只有 `search.bar` 会被替换。

## 细粒度选择器

您可以使用 `useRouterState`、`useSearch` 等钩子访问和订阅路由状态。如果只想在特定路由状态子集（如部分搜索参数）变化时重新渲染组件，可以通过 `select` 属性进行部分订阅。

```tsx
// 当 `bar` 变化时组件不会重新渲染
const foo = Route.useSearch({ select: ({ foo }) => foo })
```

### 结合细粒度选择器的结构共享

`select` 函数可以对路由状态执行各种计算，允许返回不同类型的值（如对象）。例如：

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      foo: search.foo,
      hello: `hello ${search.foo}`,
    }
  },
})
```

虽然这可行，但会导致组件每次都被重新渲染，因为 `select` 每次调用都返回新对象。

您可以通过上述"结构共享"技术避免此问题。默认情况下，为保持向后兼容性，结构共享是关闭的，但这可能在 v2 版本中改变。

启用细粒度选择器的结构共享有两种方式：

#### 在路由选项中默认启用：

```tsx
const router = createRouter({
  routeTree,
  defaultStructuralSharing: true,
})
```

#### 在每次钩子调用时启用：

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      foo: search.foo,
      hello: `hello ${search.foo}`,
    }
  },
  structuralSharing: true,
})
```

> [!重要]
> 结构共享仅适用于 JSON 兼容数据。这意味着如果启用了结构共享，您不能通过 `select` 返回类实例等对象。

遵循 TanStack Router 的类型安全设计，如果您尝试以下操作，TypeScript 会报错：

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      date: new Date(),
    }
  },
  structuralSharing: true,
})
```

如果在路由选项中默认启用了结构共享，可以通过设置 `structuralSharing: false` 来避免此错误。
