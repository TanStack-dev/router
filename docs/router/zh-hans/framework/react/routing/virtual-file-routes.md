---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:20:02.000Z
title: 虚拟文件路由
id: virtual-file-routes
---

> 我们要感谢 Remix 团队[开创了虚拟文件路由的概念](https://www.youtube.com/watch?v=fjTX8hQTlEc&t=730s)。我们从中汲取灵感，并将其适配到 TanStack Router 现有的基于文件的路由树生成机制中。

虚拟文件路由是一个强大的概念，允许您通过代码引用项目中的真实文件来编程式构建路由树。这在以下场景中尤为有用：

- 您希望保留现有的路由组织结构
- 需要自定义路由文件的存放位置
- 想要完全覆盖 TanStack Router 基于文件的路由生成机制并创建自己的约定

以下是一个快速示例，展示如何使用虚拟文件路由将路由树映射到项目中的真实文件：

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

## 配置方式

虚拟文件路由可以通过以下两种方式配置：

- 用于 Vite/Rspack/Webpack 的 `TanStackRouter` 插件
- TanStack Router CLI 的 `tsr.config.json` 文件

## 通过 TanStackRouter 插件配置

如果使用 Vite/Rspack/Webpack 的 `TanStackRouter` 插件，可以在插件初始化时通过 `virtualRoutesConfig` 选项指定路由文件路径：

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

或者直接在配置中定义虚拟路由：

```tsx
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'
import { rootRoute } from '@tanstack/virtual-file-routes'

const routes = rootRoute('root.tsx', [
  // ... 其余虚拟路由树
])

export default defineConfig({
  plugins: [TanStackRouterVite({ virtualRouteConfig: routes }), react()],
})
```

## 创建虚拟文件路由

要创建虚拟文件路由，需要导入 `@tanstack/virtual-file-routes` 包。该包提供了一系列函数来创建引用项目中真实文件的虚拟路由。主要导出的工具函数包括：

- `rootRoute` - 创建虚拟根路由
- `route` - 创建虚拟路由
- `index` - 创建虚拟索引路由
- `layout` - 创建无路径布局路由
- `physical` - 创建物理虚拟路由（后续详述）

## 虚拟根路由

`rootRoute` 函数用于创建虚拟根路由，接收文件名和子路由数组。示例：

```tsx
// routes.ts
import { rootRoute } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  // ... 子路由
])
```

## 虚拟路由

`route` 函数创建虚拟路由，接收路径、文件名和子路由数组。示例：

```tsx
// routes.ts
import { route } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  route('/about', 'about.tsx', [
    // ... 子路由
  ]),
])
```

也可以不指定文件名，这样可以为子路由设置公共路径前缀：

```tsx
// routes.ts
import { route } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  route('/hello', [
    route('/world', 'world.tsx'), // 完整路径将是 "/hello/world"
    route('/universe', 'universe.tsx'), // 完整路径将是 "/hello/universe"
  ]),
])
```

## 虚拟索引路由

`index` 函数创建虚拟索引路由，接收文件名。示例：

```tsx
import { index } from '@tanstack/virtual-file-routes'

const routes = rootRoute('root.tsx', [index('index.tsx')])
```

## 无路径虚拟路由

`layout` 函数创建无路径虚拟路由，接收文件名、子路由数组和可选的布局ID。示例：

```tsx
// routes.ts
import { layout } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  layout('pathlessLayout.tsx', [
    // ... 子路由
  ]),
])
```

也可以通过布局ID为路由指定不同于文件名的唯一标识符：

```tsx
// routes.ts
import { layout } from '@tanstack/virtual-file-routes'

export const routes = rootRoute('root.tsx', [
  layout('my-pathless-layout-id', 'pathlessLayout.tsx', [
    // ... 子路由
  ]),
])
```

## 物理虚拟路由

物理虚拟路由用于将遵循标准 TanStack Router 文件路由约定的目录"挂载"到特定URL路径下。当您希望在高层级使用虚拟路由定制部分路由树，同时对子路由继续使用标准文件路由约定时，这非常有用。

考虑以下文件结构：

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

使用虚拟路由定制除 `posts` 外的路由树，然后通过物理虚拟路由将 `posts` 目录挂载到 `/posts` 路径：

```tsx
// routes.ts
export const routes = rootRoute('root.tsx', [
  // 正常设置虚拟路由
  index('index.tsx'),
  layout('pathlessLayout.tsx', [
    route('/dashboard', 'app/dashboard.tsx', [
      index('app/dashboard-index.tsx'),
      route('/invoices', 'app/dashboard-invoices.tsx', [
        index('app/invoices-index.tsx'),
        route('$id', 'app/invoice-detail.tsx'),
      ]),
    ]),
    // 将 posts 目录挂载到 /posts 路径
    physical('/posts', 'posts'),
  ]),
])
```

## 在文件路由中使用虚拟路由

前文展示了如何在虚拟路由配置中使用标准文件路由约定。反过来也同样可行——您可以使用文件路由约定构建主路由树，同时对特定子树启用虚拟路由配置。

考虑以下文件结构：

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

`bar` 目录中包含的特殊文件 `__virtual.ts` 会指示生成器对该目录（及其子目录）切换至虚拟文件路由配置。

`__virtual.ts` 使用与前述相同的API配置该子树路由，唯一区别是不需要定义 `rootRoute`：

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

辅助函数 `defineVirtualSubtreeConfig` 借鉴了vite的 `defineConfig` 设计，允许通过默认导出来定义子树配置。默认导出可以是：

- 子树配置对象
- 返回子树配置对象的函数
- 返回子树配置对象的异步函数

## 深度嵌套

您可以自由混合使用文件路由约定和虚拟路由配置。让我们看个更复杂的例子：从文件路由约定开始，对 `/posts` 切换至虚拟路由配置，对 `/posts/lets-go` 切换回文件路由约定，再对 `/posts/lets-go/deeper` 重新启用虚拟路由配置。

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

## 通过 CLI 配置

如果使用 TanStack Router CLI，可以在 `tsr.config.json` 中配置虚拟文件路由：

```json
// tsr.config.json
{
  "virtualRouteConfig": "./routes.ts"
}
```

也可以直接在配置中定义虚拟路由（较少使用），通过向 `tsr.config.json` 添加 `virtualRouteConfig` 对象，并使用 `@tanstack/virtual-file-routes` 包中函数生成的JSON：

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
