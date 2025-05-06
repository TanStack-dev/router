---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-06T17:40:18.671Z'
id: errorComponentComponent
title: ErrorComponent component
---

`ErrorComponent` 组件用于渲染错误信息，并可选择性地显示错误的具体消息内容。

## ErrorComponent 属性

`ErrorComponent` 组件接收以下属性：

### `props.error` 属性

- 类型: `any`
- 由组件子元素抛出的错误对象

### `props.reset` 属性

- 类型: `() => void`
- 用于以编程方式重置错误状态的函数

## ErrorComponent 返回值

- 返回格式化后的错误信息，若存在错误消息则一并显示。
- 可通过点击"显示错误"按钮切换错误消息的可见性。
- 在开发环境下默认会显示错误消息。
