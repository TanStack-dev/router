---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:04.603Z'
id: createRouteMaskFunction
title: createRouteMask function
---

`createRouteMask` 函式是一個輔助函式，可用於建立路由遮罩配置，該配置可傳遞至 `RouterOptions.routeMasks` 選項。

## createRouteMask 選項

- 類型: [`RouteMask`](./RouteMaskType.md)
- 必填
- 用於配置路由遮罩的選項

## createRouteMask 回傳值

- 回傳一個具有 [`RouteMask`](./RouteMaskType.md) 類型簽章的物件，可傳遞至 `RouterOptions.routeMasks` 選項。

## 範例

```tsx
import { createRouteMask, createRouter } from '@tanstack/react-router'

const photoModalToPhotoMask = createRouteMask({
  routeTree,
  from: '/photos/$photoId/modal',
  to: '/photos/$photoId',
  params: true,
})

// 設定 Router 實例
const router = createRouter({
  routeTree,
  routeMasks: [photoModalToPhotoMask],
})
```
