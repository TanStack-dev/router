---
source-updated-at: 2025-04-01T08:21:46.000Z
translation-updated-at: 2025-04-05T03:29:45.000Z
title: 文档头管理
---

文档头部管理是指管理文档的 head、title、meta、link 和 script 标签的过程。TanStack Router 为使用 Start 的全栈应用和使用 `@tanstack/react-router` 的单页应用提供了强大的文档头部管理能力，具体包括：

- 自动去重 `title` 和 `meta` 标签
- 根据路由可见性自动加载/卸载标签
- 提供可组合的方式合并嵌套路由中的 `title` 和 `meta` 标签

对于使用 Start 的全栈应用，甚至是使用 `@tanstack/react-router` 的单页应用，文档头部管理都是至关重要的，主要原因包括：

- SEO
- 社交媒体分享
- 数据分析
- CSS 和 JS 的加载/卸载

要管理文档头部，你需要渲染 `<HeadContent />` 和 `<Scripts />` 组件，并使用 `routeOptions.head` 属性来管理路由的头部，该属性返回一个包含 `title`、`meta`、`links` 和 `scripts` 属性的对象。

## 管理文档头部

```tsx
export const Route = createRootRoute({
  head: () => ({
    meta: [
      {
        name: 'description',
        content: 'My App is a web application',
      },
      {
        title: 'My App',
      },
    ],
    links: [
      {
        rel: 'icon',
        href: '/favicon.ico',
      },
    ],
    scripts: [
      {
        src: 'https://www.google-analytics.com/analytics.js',
      },
    ],
  }),
})
```

### 去重

TanStack Router 默认会对 `title` 和 `meta` 标签进行去重，优先使用嵌套路由中**最后**出现的标签。

- 嵌套路由中定义的 `title` 标签会覆盖父路由中定义的 `title` 标签（但你可以将它们组合在一起，本指南后续部分会介绍）
- 具有相同 `name` 或 `property` 的 `meta` 标签会被嵌套路由中最后出现的标签覆盖

### `<HeadContent />`

`<HeadContent />` 组件是**必需**的，用于渲染文档的 head、title、meta、link 和 head 相关的 script 标签。

它应该**渲染在根布局的 `<head>` 标签中，或者尽可能靠近组件树的顶部**（如果你的应用无法管理 `<head>` 标签）。

### Start/全栈应用

```tsx
import { HeadContent } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => (
    <html>
      <head>
        <HeadContent />
      </head>
      <body>
        <Outlet />
      </body>
    </html>
  ),
})
```

### 单页应用

```tsx
import { HeadContent } from '@tanstack/react-router'

const rootRoute = createRootRoute({
  component: () => (
    <>
      <HeadContent />
      <Outlet />
    </>
  ),
})
```

## 管理 Body 脚本

除了可以在 `<head>` 标签中渲染的脚本外，你还可以使用 `routeOptions.scripts` 属性在 `<body>` 标签中渲染脚本。这对于需要在 DOM 加载后但在应用主入口点（包括使用 Start 或 TanStack Router 全栈实现时的 hydration）之前加载的脚本（甚至是内联脚本）非常有用。

要实现这一点，你需要：

- 使用 `routeOptions` 对象的 `scripts` 属性
- [渲染 `<Scripts />` 组件](#scripts)

```tsx
export const Route = createRootRoute({
  scripts: [
    {
      children: 'console.log("Hello, world!")',
    },
  ],
})
```

### `<Scripts />`

`<Scripts />` 组件是**必需**的，用于渲染文档的 body 脚本。它应该渲染在根布局的 `<body>` 标签中，或者尽可能靠近组件树的顶部（如果你的应用无法管理 `<body>` 标签）。

### 示例

```tsx
import { createFileRoute, Scripts } from '@tanstack/react-router'
export const Router = createFileRoute('/')({
  component: () => (
    <html>
      <head />
      <body>
        <Outlet />
        <Scripts />
      </body>
    </html>
  ),
})
```

```tsx
import { Scripts, createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => (
    <>
      <Outlet />
      <Scripts />
    </>
  ),
})
```
