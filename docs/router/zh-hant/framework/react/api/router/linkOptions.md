---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:50.570Z'
id: linkOptions
title: Link options
---

`linkOptions` 是一個函式，用於對物件字面量進行型別檢查，主要目的是供 `Link`、`navigate` 或 `redirect` 使用。

## linkOptions 屬性

`linkOptions` 接受以下選項：

### `...props`

- 型別：`LinkProps & React.RefAttributes<HTMLAnchorElement>`
- [`LinkProps`](./LinkPropsType.md)

## `linkOptions` 回傳值

一個物件字面量，其型別會根據輸入的內容自動推論

## 範例

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
