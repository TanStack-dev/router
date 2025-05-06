---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:55:21.989Z'
id: FileRouteClass
title: FileRoute class
---

> [!CAUTION]
> 该类已被弃用，并将在 TanStack Router 的下一个主要版本中移除。
> 请改用 [`createFileRoute`](./createFileRouteFunction.md) 函数。

`FileRoute` 类是一个工厂类，可用于创建基于文件的 路由 (route) 实例。该路由实例随后可用于通过 `tsr generate` 和 `tsr watch` 命令自动生成路由树。

## `FileRoute` 构造函数

`FileRoute` 构造函数接受一个参数：路由将为其生成的文件 `path`。

### 构造函数选项

- 类型：`string` 字面量
- 必需，但**由 `tsr generate` 和 `tsr watch` 命令自动插入和更新**。
- 路由将从中生成的文件的完整路径。

### 构造函数返回值

- 一个 `FileRoute` 类的实例，可用于创建路由。

## `FileRoute` 方法

`FileRoute` 类实现了以下方法：

### `.createRoute` 方法

`createRoute` 方法可用于配置文件路由实例。它接受一个参数：用于配置文件路由实例的 `options`。

#### .createRoute 选项

- 类型：`Omit<RouteOptions, 'getParentRoute' | 'path' | 'id'>`
- [`RouteOptions`](./RouteOptionsType.md)
- 可选
- 与 `Route` 类相同的选项，但省略了 `getParentRoute`、`path` 和 `id` 选项，因为这些选项对于基于文件的路由是不必要的。

#### .createRoute 返回值

一个 [`Route`](./RouteType.md) 实例，可用于配置要插入到路由树中的路由。

> ⚠️ 注意：为了使 `tsr generate` 和 `tsr watch` 正常工作，文件路由实例必须使用 `Route` 标识符从文件中导出。

### 示例

```tsx
import { FileRoute } from '@tanstack/react-router'

export const Route = new FileRoute('/').createRoute({
  loader: () => {
    return 'Hello World'
  },
  component: IndexComponent,
})

function IndexComponent() {
  const data = Route.useLoaderData()
  return <div>{data}</div>
}
```
