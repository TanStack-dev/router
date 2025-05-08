---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:23:16.148Z'
id: LinkOptionsType
title: LinkOptions type
---

`LinkOptions` 類型繼承了 [`NavigateOptions`](./NavigateOptionsType.md) 類型，並包含 TanStack Router 在處理實際錨點元素屬性時可使用的額外選項。

```tsx
type LinkOptions = NavigateOptions & {
  target?: HTMLAnchorElement['target']
  activeOptions?: ActiveOptions
  preload?: false | 'intent'
  preloadDelay?: number
  disabled?: boolean
}
```

## LinkOptions 屬性

`LinkOptions` 物件接受/包含以下屬性：

### `target`

- 類型: `HTMLAnchorElement['target']`
- 選填
- 標準錨點標籤的 target 屬性

### `activeOptions`

- 類型: `ActiveOptions`
- 選填
- 用於判斷連結是否處於啟用狀態的選項

### `preload`

- 類型: `false | 'intent' | 'viewport' | 'render'`
- 選填
- 若設定此值，連結的預載策略將被設定為該值。
- 詳情請參閱 [預載指南](../../guide/preloading.md)。

### `preloadDelay`

- 類型: `number`
- 選填
- 延遲意圖預載的毫秒數。若意圖在此延遲前退出，預載將被取消。

### `disabled`

- 類型: `boolean`
- 選填
- 若為 true，將渲染不含 href 屬性的連結
