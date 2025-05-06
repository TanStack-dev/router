---
source-updated-at: 2025-04-03T23:35:41.000Z
translation-updated-at: 2025-04-05T03:37:01.000Z
title: 滚动恢复
id: scroll-restoration
---

## 哈希/页面顶部滚动

开箱即用，TanStack Router 同时支持**哈希滚动**和**页面顶部滚动**，无需额外配置。

## 滚动至顶部与嵌套可滚动区域

默认情况下，滚动至顶部功能模拟浏览器的行为，这意味着成功导航后仅会将 `window` 自身滚动到顶部。然而，对于许多应用来说，由于复杂的布局，主滚动区域通常是嵌套的 div 或类似元素。如果你希望 TanStack Router 也为你滚动这些主滚动区域，可以通过 `routerOptions.scrollToTopSelectors` 添加选择器来定位它们：

```tsx
const router = createRouter({
  scrollToTopSelectors: ['#main-scrollable-area'],
})
```

这些选择器会**与 `window` 一起处理**，目前无法禁用 `window` 的滚动。

## 滚动恢复

滚动恢复是指在用户返回页面时恢复其滚动位置的过程。对于标准的基于 HTML 的网站，这通常是内置功能，但对于 SPA 应用来说可能难以复现，原因如下：

- SPA 通常使用 `history.pushState` API 进行导航，因此浏览器无法原生恢复滚动位置
- SPA 有时会异步渲染内容，因此浏览器在渲染完成前无法知晓页面高度
- SPA 有时会使用嵌套的可滚动容器来实现特定布局和功能

不仅如此，应用中通常会有多个可滚动区域，而不仅仅是 body。例如，聊天应用可能有一个可滚动的侧边栏和一个可滚动的聊天区域。在这种情况下，你需要独立恢复这两个区域的滚动位置。

为解决这一问题，TanStack Router 提供了一个滚动恢复组件和钩子，帮你处理监控、缓存和恢复滚动位置的过程。

其实现方式包括：

- 监控 DOM 中的滚动事件
- 向滚动恢复缓存注册可滚动区域
- 监听适当的路由事件以确定何时缓存和恢复滚动位置
- 将每个可滚动区域的滚动位置（包括 `window` 和 `body`）存储在缓存中
- 在成功导航后、DOM 绘制前恢复滚动位置

听起来可能很复杂，但对你来说只需这样简单配置：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  scrollRestoration: true,
})
```

> [!注意] > `<ScrollRestoration />` 组件仍然可用，但已被弃用。

## 自定义缓存键

借鉴 Remix 的滚动恢复 API，你也可以通过 `getKey` 选项自定义用于缓存可滚动区域滚动位置的键。例如，这可以用于强制使用相同的滚动位置，而不管用户的浏览器历史记录如何。

`getKey` 选项接收来自 TanStack Router 的相关 `Location` 状态，并期望你返回一个字符串以唯一标识该状态的滚动测量值。

默认的 `getKey` 是 `(location) => location.state.key!`，其中 `key` 是为历史记录中的每个条目生成的唯一键。

## 示例

你可以将滚动与路径名同步：

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  getScrollRestorationKey: (location) => location.pathname,
})
```

你可以有条件地仅同步某些路径，其余路径使用键：

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

## 阻止滚动恢复

有时你可能希望阻止滚动恢复发生。为此，你可以在以下 API 中使用 `resetScroll` 选项：

- `<Link resetScroll={false}>`
- `navigate({ resetScroll: false })`
- `redirect({ resetScroll: false })`

当 `resetScroll` 设为 `false` 时，下一次导航的滚动位置将不会被恢复（如果是导航到历史堆栈中的现有事件）或重置到顶部（如果是堆栈中的新历史事件）。

## 手动滚动恢复

大多数情况下，你无需特殊操作即可使滚动恢复工作。然而，某些情况下你可能需要手动控制滚动恢复。最常见的例子是**虚拟化列表**。

要为整个浏览器窗口内的虚拟化列表手动控制滚动恢复：

[//]: # 'VirtualizedWindowScrollRestorationExample'

```tsx
function Component() {
  const scrollEntry = useElementScrollRestoration({
    getElement: () => window,
  })

  // 使用 TanStack Virtual 虚拟化一些内容！
  const virtualizer = useWindowVirtualizer({
    count: 10000,
    estimateSize: () => 100,
    // 我们将滚动恢复条目中的 scrollY 传递给虚拟化器
    // 作为初始偏移量
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

要为特定元素手动控制滚动恢复，可以使用 `useElementScrollRestoration` 钩子和 `data-scroll-restoration-id` DOM 属性：

[//]: # 'ManualRestorationExample'

```tsx
function Component() {
  // 我们需要一个唯一的 ID 来手动恢复特定元素的滚动位置
  // 它在整个应用中应尽可能唯一
  const scrollRestorationId = 'myVirtualizedContent'

  // 使用该 ID 获取此元素的滚动条目
  const scrollEntry = useElementScrollRestoration({
    id: scrollRestorationId,
  })

  // 使用 TanStack Virtual 虚拟化一些内容！
  const virtualizerParentRef = React.useRef<HTMLDivElement>(null)
  const virtualizer = useVirtualizer({
    count: 10000,
    getScrollElement: () => virtualizerParentRef.current,
    estimateSize: () => 100,
    // 我们将滚动恢复条目中的 scrollY 传递给虚拟化器
    // 作为初始偏移量
    initialOffset: scrollEntry?.scrollY,
  })

  return (
    <div
      ref={virtualizerParentRef}
      // 将滚动恢复 ID 作为自定义属性传递给元素
      // 滚动恢复监视器会识别该属性
      data-scroll-restoration-id={scrollRestorationId}
      className="flex-1 border rounded-lg overflow-auto relative"
    >
      ...
    </div>
  )
}
```

[//]: # 'ManualRestorationExample'

## 滚动行为

要控制页面间导航时的滚动行为，可以使用 `scrollBehavior` 选项。这允许你将页面间的过渡设为即时而非平滑滚动。全局滚动恢复行为的配置支持与浏览器相同的选项，即 `smooth`（平滑）、`instant`（即时）和 `auto`（自动）（更多信息请参阅 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView#behavior)）。

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  scrollBehavior: 'instant',
})
```
