---
source-updated-at: '2025-03-09T10:56:27.000Z'
translation-updated-at: '2025-05-08T20:23:33.983Z'
id: NavigateOptionsType
title: NavigateOptions type
---

`NavigateOptions` 類型用於描述在 TanStack Router 中執行導航動作時可用的選項。

```tsx
type NavigateOptions = ToOptions & {
  replace?: boolean
  resetScroll?: boolean
  hashScrollIntoView?: boolean | ScrollIntoViewOptions
  viewTransition?: boolean | ViewTransitionOptions
  ignoreBlocker?: boolean
  reloadDocument?: boolean
  href?: string
}
```

## NavigateOptions 屬性

`NavigateOptions` 物件接受以下屬性：

### `replace`

- 類型：`boolean`
- 可選
- 預設為 `false`
- 若為 `true`，該位置將使用 `history.replace` 而非 `history.push` 提交至瀏覽器歷史記錄。

### `resetScroll`

- 類型：`boolean`
- 可選
- 預設為 `true`，因此當位置提交至瀏覽器歷史記錄後，滾動位置將重置為 0,0。
- 若為 `false`，位置提交至歷史記錄後，滾動位置將不會重置為 0,0。

### `hashScrollIntoView`

- 類型：`boolean | ScrollIntoViewOptions`
- 可選
- 預設為 `true`，因此當位置提交至歷史記錄後，會將符合 hash 的 id 元素滾動至視野中。
- 若為 `false`，位置提交至歷史記錄後，不會將符合 hash 的 id 元素滾動至視野中。
- 若提供物件，該物件將作為選項傳遞給 `scrollIntoView` 方法。
- 有關 `ScrollIntoViewOptions` 的更多資訊，請參閱 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)。

### `viewTransition`

- 類型：`boolean | ViewTransitionOptions`
- 可選
- 預設為 `false`
- 若為 `true`，導航將使用 `document.startViewTransition()` 呼叫。
- 若為 [`ViewTransitionOptions`](./ViewTransitionOptionsType.md)，路由導航將使用 `document.startViewTransition({update, types})` 呼叫，其中 `types` 為傳遞的 `ViewTransitionOptions["types"]` 字串陣列。若瀏覽器不支援 viewTransition 類型，導航將回退至一般的 `document.startTransition()`，與傳遞 `true` 時相同。
- 若瀏覽器不支援此 API，此選項將被忽略。
- 有關此函式運作方式的更多資訊，請參閱 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/startViewTransition)。
- 有關 viewTransition 類型的更多資訊，請參閱 [Google](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)。

### `ignoreBlocker`

- 類型：`boolean`
- 可選
- 預設為 `false`
- 若為 `true`，導航將忽略任何可能阻止它的攔截器。

### `reloadDocument`

- 類型：`boolean`
- 可選
- 預設為 `false`
- 若為 `true`，導航至路由內部將觸發完整頁面載入，而非傳統的 SPA 導航。

### `href`

- 類型：`string`
- 可選
- 可用於替代 `to`，導航至完整構建的 href，例如指向外部目標。

- [`ToOptions`](./ToOptionsType.md)
