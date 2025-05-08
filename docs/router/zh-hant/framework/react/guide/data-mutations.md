---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:20:10.184Z'
title: 資料變更
---

由於 TanStack Router 並不儲存或快取資料，它在資料變更 (data mutation) 中的角色微乎其微，僅限於對外部變更事件可能產生的 URL 副作用做出反應。儘管如此，我們仍整理了一些您可能覺得有用的變更相關功能及實作這些功能的函式庫。

請尋找並使用支援以下功能的變更工具：

- 處理並快取提交狀態 (submission state)
- 提供本地和全域的樂觀 UI 支援 (optimistic UI support)
- 內建鉤子 (hooks) 以連接失效機制 (invalidation) 或自動支援該機制
- 同時處理多個進行中的變更 (in-flight mutations)
- 將變更狀態組織為全域可存取的資源
- 提交狀態歷史記錄與垃圾回收 (garbage collection)

推薦的函式庫：

- [TanStack Query](https://tanstack.com/query/latest/docs/react/guides/mutations)
- [SWR](https://swr.vercel.app/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)
- [urql](https://formidable.com/open-source/urql/)
- [Relay](https://relay.dev/)
- [Apollo](https://www.apollographql.com/docs/react/)

或者甚至：

- [Zustand](https://zustand-demo.pmnd.rs/)
- [Jotai](https://jotai.org/)
- [Recoil](https://recoiljs.org/)
- [Redux](https://redux.js.org/)

與資料獲取 (data fetching) 類似，變更狀態並非一體適用的解決方案，因此您需要選擇符合您和團隊需求的方案。我們建議嘗試幾種不同的解決方案，找出最適合您的選項。

> ⚠️ 還在閱讀嗎？當談到持久性 (persistence) 時，提交狀態是個有趣的話題。您要永久保留每個變更嗎？如何知道何時該清除它？如果使用者離開畫面後又返回該怎麼辦？讓我們深入探討！

## 在變更後使 TanStack Router 失效

TanStack Router 內建短期快取機制。因此，即使我們在路由匹配 (route match) 卸載後不儲存任何資料，但若任何變更與 Router 中儲存的資料相關，當前路由匹配的資料很可能會變得過時。

當進行與載入器資料 (loader data) 相關的變更時，我們可以使用 `router.invalidate` 強制 Router 重新載入所有當前路由匹配：

```tsx
const router = useRouter()

const addTodo = async (todo: Todo) => {
  try {
    await api.addTodo()
    router.invalidate()
  } catch {
    //
  }
}
```

所有當前路由匹配的失效過程會在背景執行，因此現有資料將持續提供服務，直到新資料準備就緒，就像您導航至新路由一樣。

如果您想等待所有載入器完成後再失效，請將 `{sync: true}` 傳入 `router.invalidate`：

```tsx
const router = useRouter()

const addTodo = async (todo: Todo) => {
  try {
    await api.addTodo()
    await router.invalidate({ sync: true })
  } catch {
    //
  }
}
```

## 長期變更狀態

無論使用哪種變更函式庫，變更通常會建立與其提交相關的狀態。雖然大多數變更都是設定後即忘 (set-and-forget)，但有些變更狀態的生命週期較長，無論是為了支援樂觀 UI (optimistic UI) 還是向使用者提供關於其提交狀態的反饋。大多數狀態管理器會正確保留此提交狀態並公開它，以便顯示如載入動畫 (loading spinners)、成功訊息、錯誤訊息等 UI 元素。

讓我們考慮以下互動：

- 使用者導航至 `/posts/123/edit` 畫面以編輯貼文
- 使用者編輯 `123` 號貼文，成功後在編輯器下方看到「貼文已更新」的成功訊息
- 使用者導航至 `/posts` 畫面
- 使用者再次返回 `/posts/123/edit` 畫面

若未通知您的變更管理函式庫關於路由變更，您的提交狀態可能仍然存在，使用者在返回上一畫面時仍會看到「貼文更新成功」的訊息。這顯然不理想。顯然，我們並不打算永久保留此變更狀態，對吧？！

## 使用變更鍵 (mutation keys)

理想情況下，最簡單的方法是讓您的變更函式庫支援鍵控機制 (keying mechanism)，允許在鍵值變更時重置變更狀態：

```tsx
const routeApi = getRouteApi('/posts/$postId/edit')

function EditPost() {
  const { roomId } = routeApi.useParams()

  const sendMessageMutation = useCoolMutation({
    fn: sendMessage,
    // 當 roomId 變更時清除變更狀態
    // 包括任何提交狀態
    key: ['sendMessage', roomId],
  })

  // 發送多則訊息
  const test = () => {
    sendMessageMutation.mutate({ roomId, message: 'Hello!' })
    sendMessageMutation.mutate({ roomId, message: 'How are you?' })
    sendMessageMutation.mutate({ roomId, message: 'Goodbye!' })
  }

  return (
    <>
      {sendMessageMutation.submissions.map((submission) => {
        return (
          <div>
            <div>{submission.status}</div>
            <div>{submission.message}</div>
          </div>
        )
      })}
    </>
  )
}
```

## 使用 `router.subscribe` 方法

對於沒有鍵控機制的函式庫，我們可能需要在使用者離開畫面時手動重置變更狀態。為了解決這個問題，我們可以使用 TanStack Router 的 `invalidate` 和 `subscribe` 方法在使用者不再需要時清除變更狀態。

`router.subscribe` 方法是一個函式，用於訂閱各種路由器事件的回呼。我們在此特別使用的事件是 `onResolved` 事件。重要的是要理解，此事件在位置路徑 _變更（不僅僅是重新載入）並最終解析_ 時觸發。

這是重置舊變更狀態的理想位置。以下是一個範例：

```tsx
const router = createRouter()
const coolMutationCache = createCoolMutationCache()

const unsubscribeFn = router.subscribe('onResolved', () => {
  // 在路由變更時重置變更狀態
  coolMutationCache.clear()
})
```
