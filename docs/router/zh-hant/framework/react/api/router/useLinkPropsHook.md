---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:17.849Z'
id: useLinkPropsHook
title: useLinkProps hook
---

`useLinkProps` 鉤子 (hook) 接收一個物件作為參數，並返回一個 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 屬性物件。這些屬性可以安全地應用於錨點元素 (anchor element)，以建立一個可用於導航至新位置的連結。這包括對路徑名稱 (pathname)、搜尋參數 (search params)、哈希值 (hash) 和位置狀態 (location state) 的變更。

## useLinkProps 選項

```tsx
type UseLinkPropsOptions = ActiveLinkOptions &
  React.AnchorHTMLAttributes<HTMLAnchorElement>
```

- [`ActiveLinkOptions`](./ActiveLinkOptionsType.md)
- `useLinkProps` 選項用於建立 [`LinkProps`](./LinkPropsType.md) 物件。
- 它也繼承了 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 類型，因此傳遞給 `useLinkProps` 鉤子的任何額外屬性都會與 [`LinkProps`](./LinkPropsType.md) 物件合併。

## useLinkProps 返回值

- 一個 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 物件，可應用於錨點元素以建立可用於導航至新位置的連結
