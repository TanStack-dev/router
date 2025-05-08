---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:26.324Z'
id: catchBoundaryComponent
title: CatchBoundary component
---

`CatchBoundary` 元件是一個用於捕捉其子元件拋出錯誤的元件，它會渲染錯誤元件並可選擇性地呼叫 `onCatch` 回調函式。此元件也接受一個 `getResetKey` 函式，可用於在鍵值變更時以宣告式重置元件的狀態。

## CatchBoundary 屬性

`CatchBoundary` 元件接受以下屬性：

### `props.getResetKey` 屬性

- 類型: `() => string`
- 必填
- 此函式會回傳一個字串，當鍵值變更時將用於重置元件的狀態。

### `props.children` 屬性

- 類型: `React.ReactNode`
- 必填
- 當沒有錯誤發生時，元件會渲染這些子元件。

### `props.errorComponent` 屬性

- 類型: `React.ReactNode`
- 選填 - [`預設值: ErrorComponent`](./errorComponentComponent.md)
- 當錯誤發生時，將渲染此錯誤元件。

### `props.onCatch` 屬性

- 類型: `(error: any) => void`
- 選填
- 當子元件拋出錯誤時，會呼叫此回調函式並傳入錯誤物件。

## CatchBoundary 回傳值

- 若沒有錯誤發生，則回傳子元件。
- 若發生錯誤，則回傳 `errorComponent`。

## 範例

```tsx
import { CatchBoundary } from '@tanstack/react-router'

function Component() {
  return (
    <CatchBoundary
      getResetKey={() => 'reset'}
      onCatch={(error) => console.error(error)}
    >
      <div>My Component</div>
    </CatchBoundary>
  )
}
```
