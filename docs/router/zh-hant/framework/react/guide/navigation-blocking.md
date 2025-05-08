---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:19:58.172Z'
title: 導航阻擋
---

# 導航攔截 (Navigation Blocking)

導航攔截是一種防止導航發生的方式。通常在以下用戶嘗試導航時使用：

- 有未儲存的變更
- 正在填寫表單中
- 正在進行付款流程

在這些情況下，應向用戶顯示提示或自訂介面，確認他們是否要離開當前頁面。

- 如果用戶確認，導航將正常繼續
- 如果用戶取消，所有待處理的導航將被攔截

## 導航攔截如何運作？

導航攔截會在底層的 history API 上添加一層或多層「攔截器」。如果有任何攔截器存在，導航將通過以下方式之一暫停：

- 自訂介面
  - 如果導航是由我們在路由層級控制的內容觸發，我們可以讓您執行任何任務或向用戶顯示任何介面來確認操作。每個攔截器的 `blocker` 函式將被非同步且依序執行。如果任何攔截器函式解析或返回 `true`，導航將被允許，其他攔截器將繼續相同流程，直到所有攔截器都被允許繼續。如果任何單個攔截器解析或返回 `false`，導航將被取消，其餘的 `blocker` 函式將被忽略。
- `onbeforeunload` 事件
  - 對於我們無法直接控制的頁面事件，我們依賴瀏覽器的 `onbeforeunload` 事件。如果用戶嘗試關閉標籤頁或視窗、重新整理或以任何方式「卸載」頁面資源，瀏覽器的通用「您確定要離開嗎？」對話框將顯示。如果用戶確認，所有攔截器將被繞過，頁面將卸載。如果用戶取消，卸載將被取消，頁面將保持原狀。

## 如何使用導航攔截？

有 2 種方式使用導航攔截：

- 基於鉤子 (hook)/邏輯的攔截
- 基於元件的攔截

## 基於鉤子 (hook)/邏輯的攔截

假設我們想在表單有變更時防止導航。我們可以使用 `useBlocker` 鉤子 (hook) 來實現：

[//]: # 'HookBasedBlockingExample'

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => {
      if (!formIsDirty) return false

      const shouldLeave = confirm('Are you sure you want to leave?')
      return !shouldLeave
    },
  })

  // ...
}
```

[//]: # 'HookBasedBlockingExample'

`shouldBlockFn` 提供類型安全的 `current` 和 `next` 位置存取：

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  // always block going from /foo to /bar/123?hello=world
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ current, next }) => {
      return (
        current.routeId === '/foo' &&
        next.fullPath === '/bar/$id' &&
        next.params.id === 123 &&
        next.search.hello === 'world'
      )
    },
    withResolver: true,
  })

  // ...
}
```

您可以在 [API 參考](../api/router/useBlockerHook.md) 中找到關於 `useBlocker` 鉤子 (hook) 的更多資訊。

## 基於元件的攔截

除了基於邏輯/鉤子 (hook) 的攔截外，還可以使用 `Block` 元件來達到類似效果：

[//]: # 'ComponentBasedBlockingExample'

```tsx
import { Block } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  return (
    <Block
      shouldBlockFn={() => {
        if (!formIsDirty) return false

        const shouldLeave = confirm('Are you sure you want to leave?')
        return !shouldLeave
      }}
    />
  )

  // OR

  return (
    <Block shouldBlockFn={() => !formIsDirty} withResolver>
      {({ status, proceed, reset }) => <>{/* ... */}</>}
    </Block>
  )
}
```

[//]: # 'ComponentBasedBlockingExample'

## 如何顯示自訂介面？

在多數情況下，在 `shouldBlockFn` 函式中使用 `window.confirm` 並設置 `withResolver: false` 就足夠了，因為它會明確顯示用戶導航被攔截，並根據他們的回應來解決攔截。

然而，在某些情況下，您可能希望顯示一個自訂介面，故意減少干擾並更與您的應用設計整合。

**注意：** 如果 `withResolver` 為 `true`，`shouldBlockFn` 的返回值不會解決攔截。

### 帶解析器的鉤子 (hook)/邏輯自訂介面

[//]: # 'HookBasedCustomUIBlockingWithResolverExample'

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: () => formIsDirty,
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>Are you sure you want to leave?</p>
          <button onClick={proceed}>Yes</button>
          <button onClick={reset}>No</button>
        </div>
      )}
    </>
}
```

[//]: # 'HookBasedCustomUIBlockingWithResolverExample'

### 不帶解析器的鉤子 (hook)/邏輯自訂介面

[//]: # 'HookBasedCustomUIBlockingWithoutResolverExample'

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => {
      if (!formIsDirty) {
        return false
      }

      const shouldBlock = new Promise<boolean>((resolve) => {
        // Using a modal manager of your choice
        modals.open({
          title: 'Are you sure you want to leave?',
          children: (
            <SaveBlocker
              confirm={() => {
                modals.closeAll()
                resolve(false)
              }}
              reject={() => {
                modals.closeAll()
                resolve(true)
              }}
            />
          ),
          onClose: () => resolve(true),
        })
      })
      return shouldBlock
    },
  })

  // ...
}
```

[//]: # 'HookBasedCustomUIBlockingWithoutResolverExample'

### 基於元件的自訂介面

與鉤子 (hook) 類似，`Block` 元件返回相同的狀態和函式作為渲染屬性 (render props)：

[//]: # 'ComponentBasedCustomUIBlockingExample'

```tsx
import { Block } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  return (
    <Block shouldBlockFn={() => formIsDirty} withResolver>
      {({ status, proceed, reset }) => (
        <>
          {/* ... */}
          {status === 'blocked' && (
            <div>
              <p>Are you sure you want to leave?</p>
              <button onClick={proceed}>Yes</button>
              <button onClick={reset}>No</button>
            </div>
          )}
        </>
      )}
    </Block>
  )
}
```

[//]: # 'ComponentBasedCustomUIBlockingExample'
