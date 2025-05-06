---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:34:12.655Z'
id: useMatchHook
title: useMatch hook
---

`useMatch` 钩子返回组件树中的 [`RouteMatch`](./RouteMatchType.md) 对象。原始路由匹配包含路由器中关于路由匹配的所有信息，并且为许多其他钩子（如 `useParams`、`useLoaderData`、`useRouteContext` 和 `useSearch`）提供底层支持。

## useMatch 选项

`useMatch` 钩子接受一个参数，即 `options` 对象。

### `opts.from` 选项

- 类型: `string`
- 路由匹配的 ID
- 可选，但建议提供以实现完整的类型安全。
- 如果 `opts.strict` 为 `true`，则必须提供 `from`，否则 TypeScript 会发出警告。
- 如果 `opts.strict` 为 `false`，则不得设置 `from`，并且 TypeScript 会为返回的 [`RouteMatch`](./RouteMatchType.md) 提供宽松类型。

### `opts.strict` 选项

- 类型: `boolean`
- 可选
- `默认值: true`
- 如果为 `false`，则不得设置 `opts.from`，并且类型会放宽为 `Partial<RouteMatch>`，以反映所有匹配的共享类型。

### `opts.select` 选项

- 可选
- `(match: RouteMatch) => TSelected`
- 如果提供，此函数会接收路由匹配作为参数，其返回值将作为 `useMatch` 的返回值。此值还会用于通过浅层相等性检查确定钩子是否应重新渲染其父组件。

### `opts.structuralSharing` 选项

- 类型: `boolean`
- 可选
- 配置是否为 `select` 返回的值启用结构共享。
- 更多信息请参阅 [渲染优化指南](../../guide/render-optimizations.md)。

### `opts.shouldThrow` 选项

- 类型: `boolean`
- 可选
- `默认值: true`
- 如果为 `false`，当在当前渲染的匹配中未找到匹配项时，`useMatch` 不会抛出异常；此时会返回 `undefined`。

## useMatch 返回值

- 如果提供了 `select` 函数，则返回 `select` 函数的返回值。
- 如果未提供 `select` 函数，则返回 [`RouteMatch`](./RouteMatchType.md) 对象；如果 `opts.strict` 为 `false`，则返回宽松版本的 `RouteMatch` 对象。

## 示例

### 访问路由匹配

```tsx
import { useMatch } from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: '/posts/$postId' })
  //     ^? 严格匹配 RouteMatch
  // ...
}
```

### 访问根路由的匹配

```tsx
import {
  useMatch,
  rootRouteId, // <<<< 使用此标记！
} from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: rootRouteId })
  //     ^? 严格匹配 RouteMatch
  // ...
}
```

### 检查特定路由是否正在渲染

```tsx
import { useMatch } from '@tanstack/react-router'

function Component() {
  const match = useMatch({ from: '/posts', shouldThrow: false })
  //     ^? RouteMatch | undefined
  if (match !== undefined) {
    // ...
  }
}
```
