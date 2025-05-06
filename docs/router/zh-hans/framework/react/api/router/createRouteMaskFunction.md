---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:41:06.999Z'
id: createRouteMaskFunction
title: createRouteMask function
---

`createRouteMask` 函数是一个辅助函数，可用于创建路由掩码配置，该配置可传递给 `RouterOptions.routeMasks` 选项。

## createRouteMask 参数选项

- 类型: [`RouteMask`](./RouteMaskType.md)
- 必填
- 用于配置路由掩码的选项

## createRouteMask 返回值

- 返回一个类型签名为 [`RouteMask`](./RouteMaskType.md) 的对象，该对象可传递给 `RouterOptions.routeMasks` 选项。

## 示例

```tsx
import { createRouteMask, createRouter } from '@tanstack/react-router'

const photoModalToPhotoMask = createRouteMask({
  routeTree,
  from: '/photos/$photoId/modal',
  to: '/photos/$photoId',
  params: true,
})

// 设置 Router 实例
const router = createRouter({
  routeTree,
  routeMasks: [photoModalToPhotoMask],
})
```
