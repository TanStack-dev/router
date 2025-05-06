---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:38:39.206Z'
id: navigateComponent
title: Navigate component
---

`Navigate` 组件是一个在渲染时可用来导航至新位置的组件。这包括对路径名 (pathname)、搜索参数 (search params)、哈希值 (hash) 以及位置状态 (location state) 的变更。当成功渲染后，底层导航行为会在 `useEffect` 钩子中触发。

## Navigate 属性

`Navigate` 组件接受以下属性：

### `...options`

- 类型: [`NavigateOptions`](./NavigateOptionsType.md)

## Navigate 返回值

- `null`
