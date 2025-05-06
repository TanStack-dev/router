---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:32:45.606Z'
id: useNavigateHook
title: useNavigate hook
---

`useNavigate` 钩子 (hook) 是一个返回 `navigate` 导航函数的钩子，可用于跳转到新位置。这包括对路径名 (pathname)、搜索参数 (search params)、哈希值 (hash) 和位置状态 (location state) 的更改。

## useNavigate 选项

`useNavigate` 钩子接收一个参数，即 `options` 配置对象。

### `opts.from` 选项

- 类型: `string`
- 可选
- 描述: 指定导航的起始位置。当需要从特定位置而非当前位置跳转时，此选项非常有用。

## useNavigate 返回值

- 返回一个 `navigate` 函数，可用于导航至新位置。

## navigate 函数

`navigate` 是一个用于跳转到新位置的函数。

### navigate 函数选项

`navigate` 函数接收一个参数，即 `options` 配置对象。

- 类型: [`NavigateOptions`](./NavigateOptionsType.md)

### navigate 函数返回值

- 返回一个 `Promise`，在导航完成时解析

## 示例

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
        文章列表 (第 2 页)
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            hash: 'my-hash',
          })
        }
      >
        文章列表 (哈希定位)
      </button>
      <button
        onClick={() =>
          navigate({
            to: '/posts',
            state: { from: 'home' },
          })
        }
      >
        文章列表 (携带状态)
      </button>
    </div>
  )
}
```
