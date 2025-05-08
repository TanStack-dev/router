---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:22:00.768Z'
id: errorComponentComponent
title: ErrorComponent component
---

`ErrorComponent` 元件是一個用於渲染錯誤訊息及可選錯誤詳細訊息的元件。

## ErrorComponent 屬性

`ErrorComponent` 元件接受以下屬性：

### `props.error` 屬性

- 類型：`any`
- 由元件子層拋出的錯誤物件

### `props.reset` 屬性

- 類型：`() => void`
- 用於以程式方式重設錯誤狀態的函式

## ErrorComponent 回傳值

- 若存在錯誤訊息，則回傳格式化後的錯誤訊息。
- 可透過點擊「顯示錯誤」按鈕切換錯誤訊息的顯示狀態。
- 預設情況下，在開發環境中會顯示錯誤訊息。
