---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:16:48.249Z'
id: scroll-restoration
title: 滾動恢復
---

## 雜湊/頁面頂部滾動 (Hash/Top-of-Page Scrolling)

開箱即用，TanStack Router 支援 **雜湊滾動 (hash scrolling)** 和 **頁面頂部滾動 (top-of-page scrolling)**，無需任何額外設定。

## 滾動至頂部與巢狀可滾動區域 (Scroll-to-top & Nested Scrollable Areas)

預設情況下，滾動至頂部會模擬瀏覽器的行為，這意味著僅在成功導航後將 `window` 本身滾動至頂部。然而，對於許多應用程式來說，由於進階佈局的關係，主要的可滾動區域通常是巢狀的 div 或類似元素。如果您希望 TanStack Router 也為您滾動這些主要的可滾動區域，可以使用 `routerOptions.scrollToTopSelectors` 來指定目標選擇器：

```tsx
const router = createRouter({
  scrollToTopSelectors: ['#main-scrollable-area'],
})
```

這些選擇器會 **與 `window` 一起處理**，目前無法停用 `window` 的滾動行為。

## 滾動恢復 (Scroll Restoration)

滾動恢復是指在使用者返回頁面時恢復其滾動位置的過程。這通常是標準 HTML 網站的內建功能，但在 SPA 應用程式中可能難以實現，原因如下：

- SPA 通常使用 `history.pushState` API 進行導航，因此瀏覽器無法原生恢復滾動位置
- SPA 有時會非同步渲染內容，因此瀏覽器在渲染完成前無法知道頁面的高度
- SPA 有時會使用巢狀可滾動容器來實現特定的佈局和功能

不僅如此，應用程式中常見的情況是擁有多個可滾動區域，而不僅僅是 body。例如，聊天應用程式可能有一個可滾動的側邊欄和一個可滾動的聊天區域。在這種情況下，您會希望獨立恢復這兩個區域的滾動位置。

為了解決這個問題，TanStack Router 提供了一個滾動恢復元件和鉤子 (hook)，幫助您處理監控、快取和恢復滾動位置的過程。

其運作方式如下：

- 監控 DOM 中的滾動事件
- 向滾動恢復快取註冊可滾動區域
- 監聽適當的路由器事件以知道何時快取和恢復滾動位置
- 將每個可滾動區域的滾動位置儲存在快取中（包括 `window` 和 `body`）
- 在成功導航後、DOM 繪製前恢復滾動位置

聽起來可能很複雜，但對您來說，只需這樣簡單設定：

```tsx
import { createRouter } from '@tanstack/solid-router'

const router = createRouter({
  scrollRestoration: true,
})
```

> [!注意] > `<ScrollRestoration />` 元件仍然可用，但已被標記為棄用 (deprecated)。

## 自訂快取鍵 (Custom Cache Keys)

遵循 Remix 的滾動恢復 API，您也可以使用 `getKey` 選項自訂用於快取特定可滾動區域滾動位置的鍵 (key)。例如，這可以用於強制使用相同的滾動位置，無論使用者的瀏覽歷史如何。

`getKey` 選項會接收來自 TanStack Router 的相關 `Location` 狀態，並期望您返回一個字串以唯一識別該狀態的滾動測量值。

預設的 `getKey` 是 `(location) => location.state.key!`，其中 `key` 是為歷史記錄中的每個條目生成的唯一鍵。

## 範例 (Examples)

您可以將滾動與路徑名稱 (pathname) 同步：

```tsx
import { createRouter } from '@tanstack/solid-router'

const router = createRouter({
  getScrollRestorationKey: (location) => location.pathname,
})
```

您可以有條件地僅同步某些路徑，其餘則使用鍵：

```tsx
import { createRouter } from '@tanstack/solid-router'

const router = createRouter({
  getScrollRestorationKey: (location) => {
    const paths = ['/', '/chat']
    return paths.includes(location.pathname)
      ? location.pathname
      : location.state.key!
  },
})
```

## 防止滾動恢復 (Preventing Scroll Restoration)

有時您可能希望防止滾動恢復發生。為此，您可以在以下 API 中使用 `resetScroll` 選項：

- `<Link resetScroll={false}>`
- `navigate({ resetScroll: false })`
- `redirect({ resetScroll: false })`

當 `resetScroll` 設為 `false` 時，下一次導航的滾動位置將不會被恢復（如果是導航到歷史堆疊中的現有事件）或重置到頂部（如果是堆疊中的新歷史事件）。

## 手動滾動恢復 (Manual Scroll Restoration)

大多數情況下，您無需特別操作即可讓滾動恢復正常工作。然而，在某些情況下，您可能需要手動控制滾動恢復。最常見的例子是 **虛擬化列表 (virtualized lists)**。

若要為整個瀏覽器視窗內的虛擬化列表手動控制滾動恢復：

```tsx
function Component() {
  const scrollEntry = useElementScrollRestoration({
    getElement: () => window,
  })

  // 讓我們使用 TanStack Virtual 來虛擬化一些內容！
  const virtualizer = useWindowVirtualizer({
    count: 10000,
    estimateSize: () => 100,
    // 我們將滾動恢復條目中的 scrollY 傳遞給虛擬化器
    // 作為初始偏移量
    initialOffset: scrollEntry?.scrollY,
  })

  return (
    <div>
      {virtualizer.getVirtualItems().map(item => (
        ...
      ))}
    </div>
  )
}
```

若要為特定元素手動控制滾動恢復，您可以使用 `useElementScrollRestoration` 鉤子和 `data-scroll-restoration-id` DOM 屬性：

```tsx
function Component() {
  // 我們需要一個唯一的 ID 來為特定元素手動控制滾動恢復
  // 它在您的應用程式中應盡可能唯一
  const scrollRestorationId = 'myVirtualizedContent'

  // 我們使用該 ID 來獲取此元素的滾動條目
  const scrollEntry = useElementScrollRestoration({
    id: scrollRestorationId,
  })

  // 讓我們使用 TanStack Virtual 來虛擬化一些內容！
  let virtualizerParentRef: any
  const virtualizer = createVirtualizer({
    count: 10000,
    getScrollElement: () => virtualizerParentRef,
    estimateSize: () => 100,
    // 我們將滾動恢復條目中的 scrollY 傳遞給虛擬化器
    // 作為初始偏移量
    initialOffset: scrollEntry?.scrollY,
  })

  return (
    <div
      ref={virtualizerParentRef}
      // 我們將滾動恢復 ID 作為自訂屬性傳遞給元素
      // 滾動恢復監視器會識別該屬性
      data-scroll-restoration-id={scrollRestorationId}
      class="flex-1 border rounded-lg overflow-auto relative"
    >
      ...
    </div>
  )
}
```

## 滾動行為 (Scroll Behavior)

若要控制頁面間導航時的滾動行為，您可以使用 `scrollBehavior` 選項。這允許您將頁面間的過渡設為即時滾動，而非平滑滾動。滾動恢復行為的全域設定支援與瀏覽器相同的選項，包括 `smooth`、`instant` 和 `auto`（詳見 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView#behavior)）。

```tsx
import { createRouter } from '@tanstack/solid-router'

const router = createRouter({
  scrollBehavior: 'instant',
})
```
