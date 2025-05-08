---
source-updated-at: '2025-02-27T22:43:21.000Z'
translation-updated-at: '2025-05-08T20:17:30.858Z'
id: virtual-file-routes
title: 虛擬檔案路由
---

> 我們要感謝 Remix 團隊 [開創了虛擬檔案路由的概念](https://www.youtube.com/watch?v=fjTX8hQTlEc&t=730s)。我們從他們的工作中獲得靈感，並將其調整以配合 TanStack Router 現有的基於檔案的路由樹生成功能。

虛擬檔案路由是一個強大的概念，允許您透過程式碼參照專案中的實際檔案來建構路由樹。這在以下情況下特別有用：

- 您希望保留現有的路由組織結構。
- 您想要自訂路由檔案的位置。
- 您想完全覆蓋 TanStack Router 的基於檔案的路由生成，並建立自己的慣例。

以下是一個快速範例，展示如何使用虛擬檔案路由將路由樹映射到專案中的實際檔案：

```tsx
// routes.ts
import {
  rootRoute,
  route,
  index,
  layout,
  physical,
} from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  index('index.tsx'),
  layout('pathlessLayout.tsx', [
    route('/dashboard', 'app/dashboard.tsx', [
      index('app/dashboard-index.tsx'),
      route('/invoices', 'app/dashboard-invoices.tsx', [
        index('app/invoices-index.tsx'),
        route('$id', 'app/invoice-detail.tsx'),
      ]),
    ]),
    physical('/posts', 'posts'),
  ]),
])
```

## 設定

虛擬檔案路由可以透過以下方式進行設定：

- Vite/Rspack/Webpack 的 `TanStackRouter` 插件
- TanStack Router CLI 的 `tsr.config.json` 檔案

## 透過 TanStackRouter 插件設定

如果您使用 Vite/Rspack/Webpack 的 `TanStackRouter` 插件，可以在設定插件時將路由檔案的路徑傳遞給 `virtualRoutesConfig` 選項來設定虛擬檔案路由：

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  plugins: [
    TanStackRouterVite({
      target: 'react',
      virtualRouteConfig: './routes.ts',
    }),
    react(),
  ],
})
```

或者，您也可以直接在設定中定義虛擬路由：

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'
import { rootRoute } from '@tanstack/virtual-file-routes'

const routes = rootRoute('root.tsx', [
  // ... 其餘虛擬路由樹
])

export default defineConfig({
  plugins: [TanStackRouterVite({ virtualRouteConfig: routes }), react()],
})
```

## 建立虛擬檔案路由

要建立虛擬檔案路由，您需要導入 `@tanstack/virtual-file-routes` 套件。此套件提供了一組函數，讓您可以建立參照專案中實際檔案的虛擬路由。該套件導出了以下幾個實用函數：

- `rootRoute` - 建立虛擬根路由。
- `route` - 建立虛擬路由。
- `index` - 建立虛擬索引路由。
- `layout` - 建立虛擬無路徑 (pathless) 的佈局路由。
- `physical` - 建立實體虛擬路由（稍後詳述）。

## 虛擬根路由

`rootRoute` 函數用於建立虛擬根路由。它接受一個檔案名稱和一個子路由陣列。以下是虛擬根路由的範例：

```tsx
// routes.ts
import { rootRoute } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  // ... 子路由
])
```

## 虛擬路由

`route` 函數用於建立虛擬路由。它接受一個路徑、一個檔案名稱和一個子路由陣列。以下是虛擬路由的範例：

```tsx
// routes.ts
import { route } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  route('/about', 'about.tsx', [
    // ... 子路由
  ]),
])
```

您也可以定義一個沒有檔案名稱的虛擬路由。這允許為其子路由設定共同的路徑前綴：

```tsx
// routes.ts
import { route } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  route('/hello', [
    route('/world', 'world.tsx'), // 完整路徑將是 "/hello/world"
    route('/universe', 'universe.tsx'), // 完整路徑將是 "/hello/universe"
  ]),
])
```

## 虛擬索引路由

`index` 函數用於建立虛擬索引路由。它接受一個檔案名稱。以下是虛擬索引路由的範例：

```tsx
import { index } from '@tanstack/virtual-file-routes'

const routes = rootRoute('root.tsx', [index('index.tsx')])
```

## 虛擬無路徑路由

`layout` 函數用於建立虛擬無路徑路由。它接受一個檔案名稱、一個子路由陣列和一個可選的無路徑 ID。以下是虛擬無路徑路由的範例：

```tsx
// routes.ts
import { layout } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  layout('pathlessLayout.tsx', [
    // ... 子路由
  ]),
])
```

您也可以指定一個無路徑 ID，為路由提供一個與檔案名稱不同的唯一識別碼：

```tsx
// routes.ts
import { layout } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  layout('my-pathless-layout-id', 'pathlessLayout.tsx', [
    // ... 子路由
  ]),
])
```

## 實體虛擬路由

實體虛擬路由是一種將目錄「掛載」到特定 URL 路徑下的方式，這些目錄遵循傳統的 TanStack Router 基於檔案的路由慣例。這在您使用虛擬路由自訂路由樹高層結構的同時，又想對子路由和目錄使用標準的基於檔案的路由慣例時特別有用。

考慮以下檔案結構：

```
/routes
├── root.tsx
├── index.tsx
├── pathless.tsx
├── app
│   ├── dashboard.tsx
│   ├── dashboard-index.tsx
│   ├── dashboard-invoices.tsx
│   ├── invoices-index.tsx
│   ├── invoice-detail.tsx
└── posts
    ├── index.tsx
    ├── $postId.tsx
    ├── $postId.edit.tsx
    ├── comments/
    │   ├── index.tsx
    │   ├── $commentId.tsx
    └── likes/
        ├── index.tsx
        ├── $likeId.tsx
```

讓我們使用虛擬路由來為除 `posts` 以外的所有內容自訂路由樹，然後使用實體虛擬路由將 `posts` 目錄掛載到 `/posts` 路徑下：

```tsx
// routes.ts
export const routes = rootRoute('root.tsx', [
  // 正常設定虛擬路由
  index('index.tsx'),
  layout('pathlessLayout.tsx', [
    route('/dashboard', 'app/dashboard.tsx', [
      index('app/dashboard-index.tsx'),
      route('/invoices', 'app/dashboard-invoices.tsx', [
        index('app/invoices-index.tsx'),
        route('$id', 'app/invoice-detail.tsx'),
      ]),
    ]),
    // 將 `posts` 目錄掛載到 `/posts` 路徑下
    physical('/posts', 'posts'),
  ]),
])
```

## TanStack Router 基於檔案路由中的虛擬路由

前一節展示了如何在虛擬路由設定中使用 TanStack Router 的基於檔案的路由慣例。
然而，反過來也是可行的。  
您可以使用 TanStack Router 的基於檔案的路由慣例來設定應用程式的主要路由樹，並針對特定的子樹選擇使用虛擬路由設定。

考慮以下檔案結構：

```
/routes
├── __root.tsx
├── foo
│   ├── bar
│   │   ├── __virtual.ts
│   │   ├── details.tsx
│   │   ├── home.tsx
│   │   └── route.ts
│   └── bar.tsx
└── index.tsx
```

讓我們看看包含特殊檔案 `__virtual.ts` 的 `bar` 目錄。此檔案指示生成器切換到此目錄（及其子目錄）的虛擬檔案路由設定。

`__virtual.ts` 為該路由樹的子樹設定虛擬路由。它使用與上述相同的 API，唯一的區別是沒有為該子樹定義 `rootRoute`：

```tsx
// routes/foo/bar/__virtual.ts
import {
  defineVirtualSubtreeConfig,
  index,
  route,
} from '@tanstack/virtual-file-routes'

export default defineVirtualSubtreeConfig([
  index('home.tsx'),
  route('$id', 'details.tsx'),
])
```

輔助函數 `defineVirtualSubtreeConfig` 緊密模擬了 vite 的 `defineConfig`，允許您透過預設導出來定義子樹設定。預設導出可以是：

- 子樹設定物件
- 返回子樹設定物件的函數
- 返回子樹設定物件的非同步函數

## 深入嵌套

您可以隨意混合使用 TanStack Router 的基於檔案的路由慣例和虛擬路由設定。  
讓我們更深入一點！  
查看以下範例，它一開始使用基於檔案的路由慣例，切換到 `/posts` 的虛擬路由設定，再切回 `/posts/lets-go` 的基於檔案的路由慣例，最後又切換到 `/posts/lets-go/deeper` 的虛擬路由設定。

```
├── __root.tsx
├── index.tsx
├── posts
│   ├── __virtual.ts
│   ├── details.tsx
│   ├── home.tsx
│   └── lets-go
│       ├── deeper
│       │   ├── __virtual.ts
│       │   └── home.tsx
│       └── index.tsx
└── posts.tsx
```

## 透過 TanStack Router CLI 設定

如果您使用 TanStack Router CLI，可以在 `tsr.config.json` 檔案中定義路由檔案的路徑來設定虛擬檔案路由：

```json
// tsr.config.json
{
  "virtualRouteConfig": "./routes.ts"
}
```

或者，您也可以直接在設定中定義虛擬路由。雖然這種情況較少見，但可以透過在 `tsr.config.json` 檔案中添加 `virtualRouteConfig` 物件並定義您的虛擬路由，然後傳遞由 `@tanstack/virtual-file-routes` 套件中的 `rootRoute`/`route`/`index` 等函數生成的 JSON：

```json
// tsr.config.json
{
  "virtualRouteConfig": {
    "type": "root",
    "file": "root.tsx",
    "children": [
      {
        "type": "index",
        "file": "home.tsx"
      },
      {
        "type": "route",
        "file": "posts/posts.tsx",
        "path": "/posts",
        "children": [
          {
            "type": "index",
            "file": "posts/posts-home.tsx"
          },
          {
            "type": "route",
            "file": "posts/posts-detail.tsx",
            "path": "$postId"
          }
        ]
      },
      {
        "type": "layout",
        "id": "first",
        "file": "layout/first-pathless-layout.tsx",
        "children": [
          {
            "type": "layout",
            "id": "second",
            "file": "layout/second-pathless-layout.tsx",
            "children": [
              {
                "type": "route",
                "file": "a.tsx",
                "path": "/route-a"
              },
              {
                "type": "route",
                "file": "b.tsx",
                "path": "/route-b"
              }
            ]
          }
        ]
      }
    ]
  }
}
```
