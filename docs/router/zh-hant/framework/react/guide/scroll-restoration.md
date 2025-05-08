---
source-updated-at: '2025-04-03T23:35:41.000Z'
translation-updated-at: '2025-05-08T20:18:36.069Z'
id: scroll-restoration
title: 滾動恢復
---

## 雜湊/頁面頂部滾動 (Hash/Top-of-Page Scrolling)

開箱即用，TanStack Router 支援**雜湊滾動 (hash scrolling)** 和**頁面頂部滾動 (top-of-page scrolling)**，無需任何額外配置。

## 滾動至頂部與巢狀可滾動區域 (Scroll-to-top & Nested Scrollable Areas)

預設情況下，滾動至頂部會模擬瀏覽器的行為，這意味著在成功導航後僅會將 `window` 本身滾動至頂部。然而，對於許多應用程式來說，由於進階佈局的需求，主要的可滾動區域通常是一個巢狀的 div 或類似元素。如果你希望 TanStack Router 也能為你滾動這些主要的可滾動區域，可以使用 `routerOptions.scrollToTopSelectors` 來指定目標選擇器：

```tsx
const router = createRouter({
  scrollToTopSelectors: ['#main-scrollable-area'],
})
```

這些選擇器會**與 `window` 一起處理**，目前無法禁用 `window` 的滾動行為。

## 滾動恢復 (Scroll Restoration)

滾動恢復是指在用戶返回頁面時恢復其滾動位置的過程。這通常是標準 HTML 網站的內建功能，但在單頁應用 (SPA) 中可能難以實現，原因如下：

- SPA 通常使用 `history.pushState` API 進行導航，因此瀏覽器無法原生恢復滾動位置
- SPA 有時會非同步渲染內容，因此瀏覽器在渲染完成前無法知道頁面的高度
- SPA 有時會使用巢狀可滾動容器來實現特定的佈局和功能

不僅如此，應用程式中常常會有多個可滾動區域，而不僅僅是 body。例如，聊天應用程式可能有一個可滾動的側邊欄和一個可滾動的聊天區域。在這種情況下，你會希望獨立恢復這兩個區域的滾動位置。

為了解決這個問題，TanStack Router 提供了一個滾動恢復元件和鉤子 (hook)，幫助你監控、快取和恢復滾動位置。

其運作方式如下：

- 監控 DOM 的滾動事件
- 將可滾動區域註冊到滾動恢復快取中
- 監聽適當的路由器事件以知道何時快取和恢復滾動位置
- 將每個可滾動區域的滾動位置存儲在快取中（包括 `window` 和 `body`）
- 在成功導航後、DOM 繪製前恢復滾動位置

聽起來可能很複雜，但對你來說，只需這樣做：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  scrollRestoration: true,
})
```

> [!NOTE] > `<ScrollRestoration />` 元件仍然可用，但已被標記為棄用 (deprecated)。

## 自訂快取鍵 (Custom Cache Keys)

與 Remix 的滾動恢復 API 類似，你也可以使用 `getKey` 選項來自訂用於快取滾動位置的鍵 (key)。例如，這可以用於強制使用相同的滾動位置，而不考慮用戶的瀏覽器歷史記錄。

`getKey` 選項會接收來自 TanStack Router 的相關 `Location` 狀態，並期望你返回一個字串來唯一標識該狀態的滾動位置測量值。

預設的 `getKey` 是 `(location) => location.state.key!`，其中 `key` 是為歷史記錄中的每個條目生成的唯一鍵。

## 範例 (Examples)

你可以將滾動與路徑名稱 (pathname) 同步：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  getScrollRestorationKey: (location) => location.pathname,
})
```

你也可以僅同步某些路徑，其餘路徑則使用預設鍵：

```tsx
import { createRouter } from '@tanstack/react-router'

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

有時你可能希望防止滾動恢復發生。為此，你可以在以下 API 中使用 `resetScroll` 選項：

- `<Link resetScroll={false}>`
- `navigate({ resetScroll: false })`
- `redirect({ resetScroll: false })`

當 `resetScroll` 設為 `false` 時，下一次導航的滾動位置將不會被恢復（如果是導航到歷史記錄堆疊中的現有事件）或重置到頂部（如果是堆疊中的新歷史記錄事件）。

## 手動滾動恢復 (Manual Scroll Restoration)

大多數情況下，你不需要特別處理滾動恢復。然而，某些情況下可能需要手動控制滾動恢復。最常見的例子是**虛擬化列表 (virtualized lists)**。

若要為整個瀏覽器視窗內的虛擬化列表手動控制滾動恢復：

[//]: # 'VirtualizedWindowScrollRestorationExample'

```tsx
function Component() {
  const scrollEntry = useElementScrollRestoration({
    getElement: () => window,
  })

  // 使用 TanStack Virtual 來虛擬化一些內容！
  const virtualizer = useWindowVirtualizer({
    count: 10000,
    estimateSize: () => 100,
    // 我們將滾動恢復條目中的 scrollY 傳遞給虛擬器作為初始偏移量
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

[//]: # 'VirtualizedWindowScrollRestorationExample'

若要為特定元素手動控制滾動恢復，可以使用 `useElementScrollRestoration` 鉤子和 `data-scroll-restoration-id` DOM 屬性：

[//]: # 'ManualRestorationExample'

```tsx
function Component() {
  // 需要一個唯一 ID 來為特定元素手動控制滾動恢復
  // 該 ID 應在整個應用程式中盡可能唯一
  const scrollRestorationId = 'myVirtualizedContent'

  // 使用該 ID 來獲取此元素的滾動條目
  const scrollEntry = useElementScrollRestoration({
    id: scrollRestorationId,
  })

  // 使用 TanStack Virtual 來虛擬化一些內容！
  const virtualizerParentRef = React.useRef<HTMLDivElement>(null)
  const virtualizer = useVirtualizer({
    count: 10000,
    getScrollElement: () => virtualizerParentRef.current,
    estimateSize: () => 100,
    // 我們將滾動恢復條目中的 scrollY 傳遞給虛擬器作為初始偏移量
    initialOffset: scrollEntry?.scrollY,
  })

  return (
    <div
      ref={virtualizerParentRef}
      // 將滾動恢復 ID 作為自訂屬性傳遞給元素
      // 滾動恢復監視器會識別該屬性
      data-scroll-restoration-id={scrollRestorationId}
      className="flex-1 border rounded-lg overflow-auto relative"
    >
      ...
    </div>
  )
}
```

[//]: # 'ManualRestorationExample'

## 滾動行為 (Scroll Behavior)

若要控制頁面間導航時的滾動行為，可以使用 `scrollBehavior` 選項。這允許你讓頁面間的過渡變為即時滾動，而不是平滑滾動。全局配置的滾動恢復行為支援與瀏覽器相同的選項，包括 `smooth`、`instant` 和 `auto`（詳見 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView#behavior)）。

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  scrollBehavior: 'instant',
})
```
