---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:54:57.902Z'
id: LinkOptionsType
title: LinkOptions type
---

`LinkOptions` 类型继承自 [`NavigateOptions`](./NavigateOptionsType.md) 类型，并包含 TanStack Router 在处理实际锚元素属性时可用的额外选项。

```tsx
type LinkOptions = NavigateOptions & {
  target?: HTMLAnchorElement['target']
  activeOptions?: ActiveOptions
  preload?: false | 'intent'
  preloadDelay?: number
  disabled?: boolean
}
```

## LinkOptions 属性

`LinkOptions` 对象接受/包含以下属性：

### `target`

- 类型: `HTMLAnchorElement['target']`
- 可选
- 标准锚标签的 target 属性

### `activeOptions`

- 类型: `ActiveOptions`
- 可选
- 用于确定链接是否处于激活状态的选项

### `preload`

- 类型: `false | 'intent' | 'viewport' | 'render'`
- 可选
- 若设置该属性，链接的预加载策略将设为该值。
- 更多信息请参阅 [预加载指南](../../guide/preloading.md)。

### `preloadDelay`

- 类型: `number`
- 可选
- 将意图预加载延迟指定的毫秒数。若在延迟结束前意图退出，则预加载会被取消。

### `disabled`

- 类型: `boolean`
- 可选
- 若为 true，将渲染不带 href 属性的链接
