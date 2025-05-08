---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:17:19.712Z'
title: 靜態路由資料
---

在建立路由時，您可以選擇性地在路由選項中指定 `staticData` 屬性。這個物件可以包含任何您想要的內容，只要在建立路由時能同步取得即可。

除了能從路由本身存取此資料外，您也可以透過 `match.staticData` 屬性在任何路由匹配中存取它。

## 範例

- `posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  staticData: {
    customData: 'Hello!',
  },
})
```

接著您可以在任何能存取路由的地方取得此資料，包括能對應回其路由的匹配項。

- `__root.tsx`

```tsx
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => {
    const matches = useMatches()

    return (
      <div>
        {matches.map((match) => {
          return <div key={match.id}>{match.staticData.customData}</div>
        })}
      </div>
    )
  },
})
```

## 強制使用靜態資料

若想強制要求路由必須包含靜態資料，可以使用宣告合併 (declaration merging) 為路由的靜態選項添加型別：

```tsx
declare module '@tanstack/react-router' {
  interface StaticDataRouteOption {
    customData: string
  }
}
```

現在若嘗試建立不含 `customData` 屬性的路由，將會出現型別錯誤：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  staticData: {
    // 錯誤：類型 '{ customData: number; }' 中缺少屬性 'customData'，但類型 'StaticDataRouteOption' 中需要該屬性。ts(2741)
  },
})
```

## 可選的靜態資料

若想讓靜態資料變成可選，只需在屬性後加上 `?`：

```tsx
declare module '@tanstack/react-router' {
  interface StaticDataRouteOption {
    customData?: string
  }
}
```

只要 `StaticDataRouteOption` 中有任何必填屬性，您就必須傳入一個物件。
