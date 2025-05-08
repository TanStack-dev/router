---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:12.815Z'
id: LinkPropsType
title: LinkProps type
---

`LinkProps` 型別繼承了 [`ActiveLinkOptions`](./ActiveLinkOptionsType.md) 和 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 型別，並包含 `Link` 元件專用的額外屬性。

```tsx
type LinkProps = ActiveLinkOptions &
  Omit<React.AnchorHTMLAttributes<HTMLAnchorElement>, 'children'> & {
    children?:
      | React.ReactNode
      | ((state: { isActive: boolean }) => React.ReactNode)
  }
```

## LinkProps 屬性

- 來自 [`ActiveLinkOptions`](./ActiveLinkOptionsType.md) 的所有屬性
- 來自 `React.AnchorHTMLAttributes<HTMLAnchorElement>` 的所有屬性

#### `children`

- 型別: `React.ReactNode | ((state: { isActive: boolean }) => React.ReactNode)`
- 可選
- 將在錨點元素 (anchor element) 內部渲染的子元素。如果提供的是函式，它將被呼叫並傳入一個包含 `isActive` 布林值的物件，可用於判斷連結是否處於啟用狀態。
