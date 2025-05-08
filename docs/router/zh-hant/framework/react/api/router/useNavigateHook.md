---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:01.543Z'
id: useNavigateHook
title: useNavigate hook
---

`useNavigate` 鉤子 (hook) 是一個會回傳 `navigate` 函式的鉤子，可用於導航至新位置。這包括變更路徑名稱 (pathname)、搜尋參數 (search params)、哈希值 (hash) 和位置狀態 (location state)。

## useNavigate 選項

`useNavigate` 鉤子接受一個參數，即 `options` 物件。

### `opts.from` 選項

- 類型: `string`
- 可選
- 說明: 導航的起始位置。當你想從特定位置而非目前位置導航至新位置時，此選項很有用。

## useNavigate 回傳值

- 一個可用於導航至新位置的 `navigate` 函式。

## navigate 函式

`navigate` 函式是一個可用於導航至新位置的函式。

### navigate 函式選項

`navigate` 函式接受一個參數，即 `options` 物件。

- 類型: [`NavigateOptions`](./NavigateOptionsType.md)

### navigate 函式回傳值

- 一個會在導航完成時解析的 `Promise`

## 範例

```tsx
import { useNavigate } from '@tanstack/react-router'

function PostsPage() {
  const navigate = useNavigate({ from: '/posts' })
  const handleClick = () => navigate({ search: { page: 2 } })
  // ...
}

function Component() {
  const navigate = useNavigate()
  return (
    <div>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
          })
        }
      >
        文章列表
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            search: { page: 2 },
          })
        }
      >
        文章列表 (第 2 頁)
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            hash: 'my-hash',
          })
        }
      >
        文章列表 (哈希值)
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            state: { from: 'home' },
          })
        }
      >
        文章列表 (狀態)
      </button>
    </div>
  )
}
```
