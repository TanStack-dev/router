---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:21:39.173Z'
id: navigateComponent
title: Navigate component
---

`Navigate` 元件是一個在渲染時可用於導航至新位置的元件。這包括對路徑名稱 (pathname)、搜尋參數 (search params)、雜湊值 (hash) 和位置狀態 (location state) 的變更。當成功渲染時，底層導航將在 `useEffect` 鉤子 (hook) 內執行。

## Navigate 屬性

`Navigate` 元件接受以下屬性：

### `...options`

- 類型: [`NavigateOptions`](./NavigateOptionsType.md)

## Navigate 回傳值

- `null`
