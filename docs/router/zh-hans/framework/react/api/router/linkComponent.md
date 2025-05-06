---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:39:15.838Z'
id: linkComponent
title: Link component
---

`Link` 组件用于创建可导航至新位置的链接，包括对路径名 (pathname)、搜索参数 (search params)、哈希值 (hash) 及位置状态 (location state) 的变更。

## Link 属性

`Link` 组件接受以下属性：

### `...props`

- 类型: `LinkProps & React.RefAttributes<HTMLAnchorElement>`
- [`LinkProps`](./LinkPropsType.md)

## Link 返回值

返回一个可导航至新位置的锚点元素 (anchor element)。

## 示例

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
