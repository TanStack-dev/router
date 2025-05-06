---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T18:40:19.586Z'
id: createFileRouteFunction
title: createFileRoute function
---

`createFileRoute` 函数是一个工厂方法，用于创建基于文件的 route 实例。该 route 实例随后可通过 `tsr generate` 和 `tsr watch` 命令自动生成路由树。

## createFileRoute 配置选项

`createFileRoute` 函数接收一个 `string` 类型的参数，表示生成 route 的源文件路径。

### `path` 选项

- 类型: `string` 字面量
- 必填，但**由 `tsr generate` 和 `tsr watch` 命令自动插入并更新**
- 生成 route 的源文件完整路径

## createFileRoute 返回值

返回一个新函数，该函数接收 [`RouteOptions`](./RouteOptionsType.md) 类型的参数，用于配置文件 [`Route`](./RouteType.md) 实例。

> ⚠️ 注意：要使 `tsr generate` 和 `tsr watch` 正常工作，必须使用 `Route` 标识符将文件 route 实例从文件中导出。

## 示例

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
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
