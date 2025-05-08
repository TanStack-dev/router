---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:52.605Z'
id: linkComponent
title: Link component
---

`Link` 元件是一個可用於建立連結的元件，該連結可用於導航至新位置。這包括對路徑名稱 (pathname)、搜尋參數 (search params)、雜湊值 (hash) 和位置狀態 (location state) 的變更。

## Link 屬性

`Link` 元件接受以下屬性：

### `...props`

- 類型：`LinkProps & React.RefAttributes<HTMLAnchorElement>`
- [`LinkProps`](./LinkPropsType.md)

## Link 回傳值

一個可用於導航至新位置的錨點元素 (anchor element)。

## 範例

```tsx
import { Link } from '@tanstack/react-router'

function Component() {
  return (
    <Link
      to="/somewhere/$somewhereId"
      params={{ somewhereId: 'baz' }}
      search={(prev) => ({ ...prev, foo: 'bar' })}
    >
      Click me
    </Link>
  )
}
```
