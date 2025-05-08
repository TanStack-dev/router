---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:15:59.137Z'
title: 自訂連結
---

雖然在許多情況下重複代碼是可以接受的，但你可能會發現自己過於頻繁地這樣做。有時，你可能希望建立具有額外行為或樣式的橫切關注點 (cross-cutting) 元件。你也可以考慮結合使用第三方函式庫與 TanStack Router 的型別安全功能。

## `createLink` 用於橫切關注點

`createLink` 會建立一個與 `Link` 具有相同型別參數的自訂 `Link` 元件。這意味著你可以建立自己的元件，提供與 `Link` 相同的型別安全性和 TypeScript 效能。

### 基本範例

如果你想建立一個基本的自訂連結元件，可以按照以下方式操作：

```tsx
import * as Solid from 'solid-js'
import { createLink, LinkComponent } from '@tanstack/solid-router'

export const Route = createRootRoute({
  component: RootComponent,
})

type BasicLinkProps = Solid.JSX.IntrinsicElements['a'] & {
  // 添加任何你想傳遞給錨點元素的額外 props
}

const BasicLinkComponent: Solid.Component<BasicLinkProps> = (props) => (
  <a {...props} class="block px-3 py-2 text-red-700">
    {props.children}
  </a>
)

const CreatedLinkComponent = createLink(BasicLinkComponent)

export const CustomLink: LinkComponent<typeof BasicLinkComponent> = (props) => {
  return <CreatedLinkComponent preload={'intent'} {...props} />
}
```

接著，你可以像使用其他 `Link` 元件一樣使用新建立的 `Link` 元件：

```tsx
<CustomLink to={'/dashboard/invoices/$invoiceId'} params={{ invoiceId: 0 }} />
```

## 與第三方函式庫結合使用 `createLink`

以下是一些如何將 `createLink` 與第三方函式庫結合使用的範例。

### 某函式庫範例

```tsx
// TODO: 添加此範例。
```
