---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:48:21.671Z'
id: RouteType
title: Route type
---

`Route` 类型用于描述路由实例。

## `Route` 属性和方法

`Route` 实例具有以下属性和方法：

### `.addChildren` 方法

- 类型: `(children: Route[]) => this`
- 向路由实例添加子路由，并返回该路由实例（类型会更新以反映新增的子路由）。

### `.update` 方法

- 类型: `(options: Partial<UpdatableRouteOptions>) => this`
- 使用新选项更新路由实例，并返回该路由实例（类型会更新以反映新的选项）。
- 在某些情况下，为避免循环类型引用，可以在创建路由实例后更新其选项。
- ...`RouteApi` 方法

### `.lazy` 方法

- 类型: `(lazyImporter: () => Promise<Partial<UpdatableRouteOptions>>) => this`
- 使用新的懒加载导入器更新路由实例，该导入器会在加载路由时延迟解析。此方法可用于代码分割 (code splitting)。

### ...`RouteApi` 方法

- 所有来自 [`RouteApi`](./RouteApiType.md) 的方法均可使用。
