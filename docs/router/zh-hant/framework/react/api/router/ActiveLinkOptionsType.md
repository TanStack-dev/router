---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:29.216Z'
id: ActiveLinkOptionsType
title: ActiveLinkOptions type
---

`ActiveLinkOptions` 類型繼承自 [`LinkOptions`](./LinkOptionsType.md) 類型，並包含額外的選項，用於描述連結在啟用狀態時應如何設定樣式。

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

## ActiveLinkOptions 屬性

`ActiveLinkOptions` 物件接受/包含以下屬性：

### `activeProps`

- 類型：`React.AnchorHTMLAttributes<HTMLAnchorElement>`
- 可選
- 當連結為啟用狀態時，將套用至錨點元素的屬性 (props)

### `inactiveProps`

- 類型：`React.AnchorHTMLAttributes<HTMLAnchorElement>`
- 可選
- 當連結為非啟用狀態時，將套用至錨點元素的屬性 (props)
