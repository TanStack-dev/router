---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:20.829Z'
id: catchNotFoundComponent
title: CatchNotFound Component
---

`CatchNotFound` 元件是一個用於捕捉其子元件拋出的「未找到」錯誤的元件，它會渲染一個後備元件並可選擇性地呼叫 `onCatch` 回調函式。當路徑名稱變更時，它會重置狀態。

## CatchNotFound 屬性

`CatchNotFound` 元件接受以下屬性：

### `props.children` 屬性

- 類型：`React.ReactNode`
- 必填
- 當沒有錯誤時，該元件的子元件會被渲染

### `props.fallback` 屬性

- 類型：`(error: NotFoundError) => React.ReactElement`
- 選填
- 當發生錯誤時，該元件會被渲染

### `props.onCatch` 屬性

- 類型：`(error: any) => void`
- 選填
- 一個回調函式，會以子元件拋出的錯誤作為參數被呼叫

## CatchNotFound 回傳值

- 如果沒有錯誤，則回傳元件的子元件。
- 如果發生錯誤，則回傳 `fallback` 元件。

## 範例

```tsx
import { CatchNotFound } from '@tanstack/react-router'

function Component() {
  return (
    <CatchNotFound
      fallback={(error) => <p>Not found error! {JSON.stringify(error)}</p>}
    >
      <ComponentThatMightThrowANotFoundError />
    </CatchNotFound>
  )
}
```
