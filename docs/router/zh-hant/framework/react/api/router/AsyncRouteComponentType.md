---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:21.437Z'
id: AsyncRouteComponentType
title: AsyncRouteComponent type
---

`AsyncRouteComponent` 類型用於描述一個可程式碼分割 (code-split) 的路由元件，該元件可以透過 `component.preload()` 方法進行預載入 (preload)。

```tsx
type AsyncRouteComponent<TProps> = SyncRouteComponent<TProps> & {
  preload?: () => Promise<void>
}
```
