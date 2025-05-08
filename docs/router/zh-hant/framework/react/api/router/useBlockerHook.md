---
source-updated-at: '2025-03-13T22:56:02.000Z'
translation-updated-at: '2025-05-08T20:22:12.075Z'
id: useBlockerHook
title: useBlocker hook
---

`useBlocker` 方法是一個鉤子 (hook)，當滿足條件時會[阻止導航](../../guide/navigation-blocking.md)。

> ⚠️ 以下新的 `useBlocker` API 目前處於*實驗性*階段。

## useBlocker 選項

`useBlocker` 鉤子接受一個*必要*參數，即選項物件：

### `options.shouldBlockFn` 選項

- 必填
- 類型：`ShouldBlockFn`
- 此函數應返回一個 `boolean` 或 `Promise<boolean>`，告訴阻斷器是否應阻止當前導航
- 該函數會接收到類型為 `ShouldBlockFnArgs` 的參數，其中包含當前和下一路由的資訊以及執行的動作
- 可以將此函數視為告訴路由器是否應阻止導航，因此返回 `true` 表示應阻止導航，而 `false` 表示應允許導航

```ts
interface ShouldBlockFnLocation<...> {
  routeId: TRouteId
  fullPath: TFullPath
  pathname: string
  params: TAllParams
  search: TFullSearchSchema
}

type ShouldBlockFnArgs = {
  current: ShouldBlockFnLocation
  next: ShouldBlockFnLocation
  action: HistoryAction
}
```

### `options.disabled` 選項

- 選填 - 預設為 `false`
- 類型：`boolean`
- 指定是否應完全禁用阻斷器

### `options.enableBeforeUnload` 選項

- 選填 - 預設為 `true`
- 類型：`boolean | (() => boolean)`
- 告訴阻斷器是否應有時或總是阻止瀏覽器的 `beforeUnload` 事件

### `options.withResolver` 選項

- 選填 - 預設為 `false`
- 類型：`boolean`
- 指定是否應使用鉤子返回的解析器，或者是否由 `shouldBlockFn` 函數本身來解析阻斷

### `options.blockerFn` 選項 (⚠️ 已棄用)

- 選填
- 類型：`BlockerFn`
- 該函數返回一個 `boolean` 或 `Promise<boolean>`，表示是否允許導航。

### `options.condition` 選項 (⚠️ 已棄用)

- 選填 - 預設為 `true`
- 類型：`boolean`
- 當此條件為 `true` 時，會阻止導航嘗試。

## useBlocker 返回值

一個包含手動阻止和允許導航控制的物件。

- `status` - 一個字串字面量，可以是 `'blocked'` 或 `'idle'`
- `next` - 當狀態為 `blocked` 時，一個可類型窄化的物件，包含下一位置資訊
- `current` - 當狀態為 `blocked` 時，一個可類型窄化的物件，包含當前位置資訊
- `action` - 當狀態為 `blocked` 時，一個 `HistoryAction` 字串，顯示觸發導航的動作
- `proceed` - 當狀態為 `blocked` 時，一個允許導航繼續的函數
- `reset` - 當狀態為 `blocked` 時，一個取消導航的函數（`status` 將重置為 `'idle'`）

或者

當 `withResolver` 為 `false` 時返回 `void`

## 範例

`useBlocker` 鉤子的兩個常見用例是：

### 基本用法

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => formIsDirty,
  })

  // ...
}
```

### 自訂 UI

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  const { proceed, reset, status, next } = useBlocker({
    shouldBlockFn: () => formIsDirty,
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>You are navigating to {next.pathname}</p>
          <p>Are you sure you want to leave?</p>
          <button onClick={proceed}>Yes</button>
          <button onClick={reset}>No</button>
        </div>
      )}
    </>
}
```

### 條件式阻止

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ next }) => {
      return !next.pathname.includes('step/')
    },
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
  )
}
```

### 不使用解析器

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: ({ next }) => {
      if (next.pathname.includes('step/')) {
        return false
      }

      const shouldLeave = confirm('Are you sure you want to leave?')
      return !shouldLeave
    },
  })

  // ...
}
```

### 類型窄化

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  // block going from editor-1 to /foo/123?hello=world
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ current, next }) => {
      if (
        current.routeId === '/editor-1' &&
        next.fullPath === '/foo/$id' &&
        next.params.id === '123' &&
        next.search.hello === 'world'
      ) {
        return true
      }
      return false
    },
    enableBeforeUnload: false,
    withResolver: true,
  })

  // ...
}
```
