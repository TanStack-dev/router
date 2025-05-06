---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:55:41.366Z'
id: ActiveLinkOptionsType
title: ActiveLinkOptions type
---

`ActiveLinkOptions` 类型扩展了 [`LinkOptions`](./LinkOptionsType.md) 类型，并包含额外的选项，用于描述链接在激活状态时应如何被样式化。

```tsx
type ActiveLinkOptions = LinkOptions & {
  activeProps?:
    | React.AnchorHTMLAttributes<HTMLAnchorElement>
    | (() => React.AnchorHTMLAttributes<HTMLAnchorElement>)
  inactiveProps?:
    | React.AnchorHTMLAttributes<HTMLAnchorElement>
    | (() => React.AnchorHTMLAttributes<HTMLAnchorElement>)
}
```

## ActiveLinkOptions 属性

`ActiveLinkOptions` 对象接受/包含以下属性：

### `activeProps`

- 类型: `React.AnchorHTMLAttributes<HTMLAnchorElement>`
- 可选
- 当链接处于激活状态时，将应用于锚点元素的属性

### `inactiveProps`

- 类型: `React.AnchorHTMLAttributes<HTMLAnchorElement>`
- 可选
- 当链接处于非激活状态时，将应用于锚点元素的属性
