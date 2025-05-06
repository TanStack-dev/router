---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:39:30.794Z'
id: lazyRouteComponentFunction
title: lazyRouteComponent function
---

> [!IMPORTANT]
> 如果您正在使用基于文件的路由 (file-based routing)，建议改用 `createLazyFileRoute` 函数。

`lazyRouteComponent` 函数可用于创建一个可代码分割 (code-split) 的路由组件，该组件可以通过 `component.preload()` 方法进行预加载。

## lazyRouteComponent 选项

`lazyRouteComponent` 函数接受两个参数：

### `importer` 选项

- 类型: `() => Promise<T>`
- 必需
- 一个返回 Promise 的函数，该 Promise 解析为包含待加载组件的对象。

### `exportName` 选项

- 类型: `string`
- 可选
- 从导入对象中加载的组件名称。默认为 `'default'`。

## lazyRouteComponent 返回值

- 一个 `React.lazy` 组件，可通过 `component.preload()` 方法进行预加载。

## 示例

```tsx
import { lazyRouteComponent } from '@tanstack/react-router'

const route = createRoute({
  path: '/posts/$postId',
  component: lazyRouteComponent(() => import('./Post')), // 默认导出
})

// 或

const route = createRoute({
  path: '/posts/$postId',
  component: lazyRouteComponent(
    () => import('./Post'),
    'PostByIdPageComponent', // 命名导出
  ),
})
```
