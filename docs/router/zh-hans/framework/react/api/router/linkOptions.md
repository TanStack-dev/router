---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:39:05.032Z'
id: linkOptions
title: Link options
---

`linkOptions` 是一个用于类型检查对象字面量的函数，专为 `Link`、`navigate` 或 `redirect` 设计。

## linkOptions 属性

`linkOptions` 接收以下选项：

### `...props`

- 类型: `LinkProps & React.RefAttributes<HTMLAnchorElement>`
- [`LinkProps`](./LinkPropsType.md)

## `linkOptions` 返回值

一个与输入类型完全匹配的对象字面量

## 示例

```tsx
const userLinkOptions = linkOptions({
  to: '/dashboard/users/user',
  search: {
    usersView: {
      sortBy: 'email',
      filterBy: 'filter',
    },
    userId: 0,
  },
})

function DashboardComponent() {
  return <Link {...userLinkOptions} />
}
```
