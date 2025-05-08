---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:37.396Z'
id: RouteMaskType
title: RouteMask type
---

`RouteMask` 類型擴展了 [`ToOptions`](./ToOptionsType.md) 類型，並包含其他必要的屬性來建立路由遮罩 (Route Mask)。

## RouteMask 屬性

`RouteMask` 類型接受一個包含以下屬性的物件：

### `...ToOptions`

- 類型: [`ToOptions`](./ToOptionsType.md)
- 必填
- 用於配置路由遮罩 (Route Mask) 的選項

### `options.routeTree`

- 類型: `TRouteTree`
- 必填
- 此路由遮罩 (Route Mask) 將支援的路由樹 (Route Tree)

### `options.unmaskOnReload`

- 類型: `boolean`
- 選填
- 若為 `true`，當頁面重新載入時，路由遮罩 (Route Mask) 將會被移除
