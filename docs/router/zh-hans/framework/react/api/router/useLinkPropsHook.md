---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:35:17.992Z'
id: useLinkPropsHook
title: useLinkProps hook
---

`useLinkProps` 钩子函数接收一个对象作为参数，并返回一个 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 属性对象。这些属性可以安全地应用于锚元素，以创建一个可用于导航至新位置的链接。这包括对路径名 (pathname)、搜索参数 (search params)、哈希值 (hash) 和位置状态 (location state) 的变更。

## useLinkProps 选项

```tsx
type UseLinkPropsOptions = ActiveLinkOptions &
  React.AnchorHTMLAttributes<HTMLAnchorElement>
```

- [`ActiveLinkOptions`](./ActiveLinkOptionsType.md)
- `useLinkProps` 选项用于构建 [`LinkProps`](./LinkPropsType.md) 对象。
- 它同时扩展了 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 类型，因此传递给 `useLinkProps` 钩子的任何额外属性都将与 [`LinkProps`](./LinkPropsType.md) 对象合并。

## useLinkProps 返回值

- 一个 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 对象，可应用于锚元素以创建可用于导航至新位置的链接
