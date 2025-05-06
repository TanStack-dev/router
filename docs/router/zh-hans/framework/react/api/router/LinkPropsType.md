---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:54:42.634Z'
id: LinkPropsType
title: LinkProps type
---

`LinkProps` 类型继承自 [`ActiveLinkOptions`](./ActiveLinkOptionsType.md) 和 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 类型，并包含 `Link` 组件特有的额外属性。

```tsx
type LinkProps = ActiveLinkOptions &
  Omit<React.AnchorHTMLAttributes<HTMLAnchorElement>, 'children'> & {
    children?:
      | React.ReactNode
      | ((state: { isActive: boolean }) => React.ReactNode)
  }
```

## LinkProps 属性

- 包含 [`ActiveLinkOptions`](./ActiveLinkOptionsType.md) 的所有属性
- 包含 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 的所有属性

#### `children`

- 类型: `React.ReactNode | ((state: { isActive: boolean }) => React.ReactNode)`
- 可选
- 将在锚点元素内部渲染的子元素。如果提供的是函数，它会被调用并传入包含 `isActive` 布尔值的对象，该值可用于判断链接是否处于激活状态。
