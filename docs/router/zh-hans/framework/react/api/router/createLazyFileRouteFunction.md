---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:42:22.738Z'
id: createLazyFileRouteFunction
title: createLazyFileRoute function
---

`createLazyFileRoute` 函数用于创建一个基于文件的部分路由实例，该实例会在匹配时延迟加载。此路由实例仅可用于配置路由的 [非关键属性](../../guide/code-splitting.md#how-does-tanstack-router-split-code)，例如 `component`、`pendingComponent`、`errorComponent` 和 `notFoundComponent`。

## createLazyFileRoute 选项

`createLazyFileRoute` 函数接受一个类型为 `string` 的参数，表示路由生成来源文件的 `path`。

### `path`

- 类型: `string`
- 必填，但 **由 `tsr generate` 和 `tsr watch` 命令自动插入并更新**
- 路由生成来源文件的完整路径。

### createLazyFileRoute 返回值

返回一个新函数，该函数接受一个 [`RouteOptions`](./RouteOptionsType.md) 类型的部分参数，用于配置文件 [`Route`](./RouteType.md) 实例。

- 类型:

```tsx
Pick<
  RouteOptions,
  'component' | 'pendingComponent' | 'errorComponent' | 'notFoundComponent'
>
```

- [`RouteOptions`](./RouteOptionsType.md)

> ⚠️ 注意：为了使 `tsr generate` 和 `tsr watch` 正常工作，文件路由实例必须使用 `Route` 标识符从文件中导出。

### 示例

```tsx
import { createLazyFileRoute } from '@tanstack/react-router'

export const Route = createLazyFileRoute('/')({
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return <div>{data}</div>
}
```
