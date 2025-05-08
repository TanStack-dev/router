---
source-updated-at: '2025-02-25T21:17:33.000Z'
translation-updated-at: '2025-05-08T20:25:17.825Z'
id: static-server-functions
title: 靜態伺服器函式 (Static Server Functions)
---

## 什麼是靜態伺服器函式 (Static Server Functions)？

靜態伺服器函式 (Static Server Functions) 是在建置時執行並在使用預渲染 (prerendering)/靜態生成 (static-generation) 時快取為靜態資源的伺服器函式。透過將 `type: 'static'` 選項傳遞給 `createServerFn`，可將其設定為「靜態」模式：

```tsx
const myServerFn = createServerFn({ type: 'static' }).handler(async () => {
  return 'Hello, world!'
})
```

此模式的運作流程如下：

- 建置階段 (Build-time)
  - 在建置階段預渲染時，會執行帶有 `type: 'static'` 的伺服器函式
  - 結果會與建置輸出一起快取為靜態 JSON 檔案，並以衍生金鑰 (函式 ID + 參數/負載雜湊) 儲存
  - 結果會在預渲染/靜態生成期間正常返回，並用於預渲染頁面
- 執行階段 (Runtime)
  - 初始時，會提供預渲染頁面的 HTML，並將伺服器函式資料嵌入 HTML 中
  - 當客戶端掛載時，會水合 (hydrate) 嵌入的伺服器函式資料
  - 對於未來的客戶端呼叫，伺服器函式會被替換為對靜態 JSON 檔案的 fetch 呼叫

## 自訂伺服器函式靜態快取

預設情況下，靜態伺服器函式快取實作會透過 node 的 `fs` 模組在建置輸出目錄中儲存和檢索靜態資料，並在執行階段使用 `fetch` 呼叫相同的靜態檔案來獲取資料。

可以透過匯入並呼叫 `createServerFnStaticCache` 函式來建立自訂快取實作，然後呼叫 `setServerFnStaticCache` 進行設定，從而自訂此介面：

```tsx
import {
  createServerFnStaticCache,
  setServerFnStaticCache,
} from '@tanstack/react-start/client'

const myCustomStaticCache = createServerFnStaticCache({
  setItem: async (ctx, data) => {
    // 將靜態資料儲存到自訂快取中
  },
  getItem: async (ctx) => {
    // 從自訂快取中檢索靜態資料
  },
  fetchItem: async (ctx) => {
    // 在執行階段從自訂快取中獲取靜態資料
  },
})

setServerFnStaticCache(myCustomStaticCache)
```
