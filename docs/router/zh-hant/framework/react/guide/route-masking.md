---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:19:10.557Z'
title: 路由遮罩
---

路由遮罩 (Route Masking) 是一種將實際路由 URL 隱藏並替換為顯示於瀏覽器歷史記錄和網址列中的技術。這適用於以下情境：您希望顯示與實際導航目標不同的 URL，並在分享該 URL 或（可選）重新載入頁面時回退至顯示的網址。以下是一些範例：

- 導航至 `/photo/5/modal` 這樣的模態路由 (modal route)，但遮罩實際 URL 為 `/photos/5`
- 導航至 `/post/5/comments` 這樣的模態路由，但遮罩實際 URL 為 `/posts/5`
- 導航至帶有搜尋參數 `?showLogin=true` 的路由，但遮罩 URL 使其不包含該參數
- 導航至帶有搜尋參數 `?modal=settings` 的路由，但遮罩 URL 為 `/settings`

這些情境均可透過路由遮罩實現，甚至可延伸支援更進階的模式，例如[平行路由 (parallel routes)](./parallel-routes.md)。

## 路由遮罩如何運作？

> [!IMPORTANT] > **無需**理解路由遮罩的運作原理即可使用。本節僅供好奇底層機制者參考。若想直接了解如何使用，請跳至[如何使用路由遮罩？](#how-do-i-use-route-masking)。

路由遮罩利用 `location.state` API 將預期的運行時位置 (runtime location) 儲存於即將寫入 URL 的位置中。此運行時位置會儲存在 `__tempLocation` 狀態屬性下：

```tsx
const location = {
  pathname: '/photos/5',
  search: '',
  hash: '',
  state: {
    key: 'wesdfs',
    __tempKey: 'sadfasd',
    __tempLocation: {
      pathname: '/photo/5/modal',
      search: '',
      hash: '',
      state: {},
    },
  },
}
```

當路由器從歷史記錄解析出帶有 `location.state.__tempLocation` 屬性的位置時，它會使用該位置而非從 URL 解析出的位置。這讓您能導航至如 `/photos/5` 的路由，而路由器實際會導航至 `/photo/5/modal`。此時，歷史記錄位置會被存回 `location.maskedLocation` 屬性，以備需要知道**實際 URL** 時使用。例如開發者工具 (Devtools) 會偵測路由是否被遮罩，並顯示實際 URL 而非遮罩後的網址！

請記住，您無需擔心這些細節，所有流程都會在底層自動處理！

## 如何使用路由遮罩？

路由遮罩提供兩種簡單的 API 使用方式：

- **命令式 (Imperative)**：透過 `<Link>` 和 `navigate()` API 的 `mask` 選項
- **宣告式 (Declarative)**：透過路由器的 `routeMasks` 選項

無論使用哪種 API，`mask` 選項接受的導航物件與 `<Link>` 和 `navigate()` API 相同。這表示您可使用熟悉的 `to`、`replace`、`state` 和 `search` 選項，唯一差別在於 `mask` 選項會用於遮罩目標路由的 URL。

> 🧠 `mask` 選項也支援**型別安全 (type-safe)**！若使用 TypeScript，當傳遞無效的導航物件至 `mask` 時，會觸發型別錯誤。太棒了！

### 命令式路由遮罩

`<Link>` 和 `navigate()` API 均接受 `mask` 選項，可用於遮罩目標路由的 URL。以下是搭配 `<Link>` 元件的範例：

```tsx
<Link
  to="/photos/$photoId/modal"
  params={{ photoId: 5 }}
  mask={{
    to: '/photos/$photoId',
    params: {
      photoId: 5,
    },
  }}
>
  開啟照片
</Link>
```

以下是搭配 `navigate()` API 的範例：

```tsx
const navigate = useNavigate()

function onOpenPhoto() {
  navigate({
    to: '/photos/$photoId/modal',
    params: { photoId: 5 },
    mask: {
      to: '/photos/$photoId',
      params: {
        photoId: 5,
      },
    },
  })
}
```

### 宣告式路由遮罩

除了命令式 API，您也可使用路由器的 `routeMasks` 選項來宣告式遮罩路由。無需在每個 `<Link>` 或 `navigate()` 呼叫傳遞 `mask` 選項，而是直接在路由器上建立路由遮罩來匹配特定模式。以下是前述範例改用 `routeMasks` 選項的實作：

```tsx
import { createRouteMask } from '@tanstack/react-router'

const photoModalToPhotoMask = createRouteMask({
  routeTree,
  from: '/photos/$photoId/modal',
  to: '/photos/$photoId',
  params: (prev) => ({
    photoId: prev.photoId,
  }),
})

const router = createRouter({
  routeTree,
  routeMasks: [photoModalToPhotoMask],
})
```

建立路由遮罩時，需傳遞至少包含以下屬性的 1 個參數：

- `routeTree`：路由遮罩將套用的路由樹 (route tree)
- `from`：路由遮罩將套用的路由 ID
- `...navigateOptions`：標準的 `to`、`search`、`params`、`replace` 等選項（與 `<Link>` 和 `navigate()` API 接受的選項相同）

> 🧠 `createRouteMask` 選項也支援**型別安全**！若使用 TypeScript，當傳遞無效的路由遮罩至 `routeMasks` 時，會觸發型別錯誤。

## 分享 URL 時解除遮罩

當 URL 被分享時會自動解除遮罩，因為一旦 URL 從瀏覽器的本地歷史記錄堆疊分離，其遮罩資料便不再可用。本質上，當您從歷史記錄複製貼上 URL 時，其遮罩資料即遺失...畢竟這正是遮罩 URL 的目的！

## 本地解除遮罩預設值

**預設情況下，本地重新載入頁面時不會解除 URL 遮罩**。遮罩資料儲存在歷史記錄位置的 `location.state` 屬性中，因此只要歷史記錄位置仍存在於歷史堆疊的記憶體內，遮罩資料就會保持可用，URL 也會持續被遮罩。

## 重新載入頁面時解除遮罩

**如前所述，預設情況下重新載入頁面時不會解除 URL 遮罩**。

若想在本地重新載入頁面時解除遮罩，您有 3 種選項（優先級依序遞增）：

1. 將路由器的預設 `unmaskOnReload` 選項設為 `true`
2. 在透過 `createRouteMask()` 建立路由遮罩時，從遮罩函式回傳 `unmaskOnReload: true` 選項
3. 傳遞 `unmaskOnReload: true` 選項至 `<Link>` 元件或 `navigate()` API
