---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:50:46.758Z'
id: RouteMaskType
title: RouteMask type
---

`RouteMask` 类型扩展了 [`ToOptions`](./ToOptionsType.md) 类型，并包含创建路由掩码 (Route Mask) 所需的其他属性。

## RouteMask 属性

`RouteMask` 类型接受一个包含以下属性的对象：

### `...ToOptions`

- 类型: [`ToOptions`](./ToOptionsType.md)
- 必填
- 用于配置路由掩码 (Route Mask) 的选项

### `options.routeTree`

- 类型: `TRouteTree`
- 必填
- 该路由掩码 (Route Mask) 将支持的路由树 (Route Tree)

### `options.unmaskOnReload`

- 类型: `boolean`
- 可选
- 如果设为 `true`，页面重新加载时将移除路由掩码 (Route Mask)
