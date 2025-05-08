---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:37.759Z'
id: RouteType
title: Route type
---

`Route` 類型用於描述一個路由實例。

## `Route` 屬性和方法

一個 `Route` 實例具有以下屬性和方法：

### `.addChildren` 方法

- 類型: `(children: Route[]) => this`
- 將子路由添加到路由實例中，並返回該路由實例（但會更新類型以反映新的子路由）。

### `.update` 方法

- 類型: `(options: Partial<UpdatableRouteOptions>) => this`
- 使用新的選項更新路由實例，並返回該路由實例（但會更新類型以反映新的選項）。
- 在某些情況下，更新已創建的路由實例選項可以避免循環類型引用。
- ...`RouteApi` 方法

### `.lazy` 方法

- 類型: `(lazyImporter: () => Promise<Partial<UpdatableRouteOptions>>) => this`
- 使用一個新的懶加載導入器更新路由實例，該導入器將在加載路由時延遲解析。這對於代碼拆分 (code splitting) 非常有用。

### ...`RouteApi` 方法

- 所有來自 [`RouteApi`](./RouteApiType.md) 的方法均可使用。
