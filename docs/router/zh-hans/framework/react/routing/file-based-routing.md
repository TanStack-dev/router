---
source-updated-at: 2025-03-28T17:52:54.000Z
translation-updated-at: 2025-04-05T03:16:12.000Z
title: 基于文件的路由
---

TanStack Router 的大部分文档都是基于文件路由编写的，旨在帮助您更详细地了解如何配置基于文件的路由及其背后的技术实现细节。虽然文件路由是配置 TanStack Router 的首选和推荐方式，但如果您更倾向于代码方式，也可以使用[基于代码的路由](./code-based-routing.md)。

## 什么是文件路由？

文件路由是一种利用文件系统来配置路由的方式。您无需通过代码定义路由结构，而是通过一系列代表应用路由层级的文件和目录来定义路由。这种方式具有以下优势：

- **简洁性**：文件路由直观易懂，无论对新开发者还是经验丰富的开发者都很友好。
- **组织性**：路由的组织方式与应用的 URL 结构完全对应。
- **可扩展性**：随着应用规模增长，文件路由能轻松添加新路由并维护现有路由。
- **代码分割**：文件路由允许 TanStack Router 自动进行路由级代码分割以提升性能。
- **类型安全**：文件路由通过生成路由类型关联显著提升了类型安全上限，而基于代码的路由实现这一过程通常十分繁琐。
- **一致性**：文件路由强制保持路由结构的一致性，使得应用维护、更新以及跨项目迁移更加轻松。

## 使用 `/` 还是 `.` ？

虽然目录长期被用于表示路由层级，但文件路由引入了在文件名中使用 `.` 字符来表示路由嵌套的新概念。这样您既可以避免为少量深层嵌套路由创建目录，又能继续用目录组织宽泛的路由层级。下面通过示例说明！

## 目录路由

目录可用于表示路由层级，既能将多个路由按逻辑分组，也能减少深层嵌套路由的文件名长度。

参考以下示例：

| 文件名                  | 路由路径                  | 组件输出                          |
| ----------------------- | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`          |                           | `<Root>`                          |
| ʦ `index.tsx`           | `/` (精确匹配)            | `<Root><RootIndex>`               |
| ʦ `about.tsx`           | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`           | `/posts`                  | `<Root><Posts>`                   |
| 📂 `posts`              |                           |                                   |
| ┄ ʦ `index.tsx`         | `/posts` (精确匹配)       | `<Root><Posts><PostsIndex>`       |
| ┄ ʦ `$postId.tsx`       | `/posts/$postId`          | `<Root><Posts><Post>`             |
| 📂 `posts_`             |                           |                                   |
| ┄ 📂 `$postId`          |                           |                                   |
| ┄ ┄ ʦ `edit.tsx`        | `/posts/$postId/edit`     | `<Root><EditPost>`                |
| ʦ `settings.tsx`        | `/settings`               | `<Root><Settings>`                |
| 📂 `settings`           |                           | `<Root><Settings>`                |
| ┄ ʦ `profile.tsx`       | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ┄ ʦ `notifications.tsx` | `/settings/notifications` | `<Root><Settings><Notifications>` |
| ʦ `_pathlessLayout.tsx` |                           | `<Root><PathlessLayout>`          |
| 📂 `_pathlessLayout`    |                           |                                   |
| ┄ ʦ `route-a.tsx`       | `/route-a`                | `<Root><PathlessLayout><RouteA>`  |
| ┄ ʦ `route-b.tsx`       | `/route-b`                | `<Root><PathlessLayout><RouteB>`  |
| 📂 `files`              |                           |                                   |
| ┄ ʦ `$.tsx`             | `/files/$`                | `<Root><Files>`                   |

## 扁平路由

扁平路由允许使用 `.` 表示路由嵌套层级。当存在大量独特的深层嵌套路由时，这种方式能避免为每个路由创建目录：

参考以下示例：

| 文件名                          | 路由路径                  | 组件输出                          |
| ------------------------------- | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`                  |                           | `<Root>`                          |
| ʦ `index.tsx`                   | `/` (精确匹配)            | `<Root><RootIndex>`               |
| ʦ `about.tsx`                   | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`                   | `/posts`                  | `<Root><Posts>`                   |
| ʦ `posts.index.tsx`             | `/posts` (精确匹配)       | `<Root><Posts><PostsIndex>`       |
| ʦ `posts.$postId.tsx`           | `/posts/$postId`          | `<Root><Posts><Post>`             |
| ʦ `posts_.$postId.edit.tsx`     | `/posts/$postId/edit`     | `<Root><EditPost>`                |
| ʦ `settings.tsx`                | `/settings`               | `<Root><Settings>`                |
| ʦ `settings.profile.tsx`        | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ʦ `settings.notifications.tsx`  | `/settings/notifications` | `<Root><Settings><Notifications>` |
| ʦ `_pathlessLayout.tsx`         |                           | `<Root><PathlessLayout>`          |
| ʦ `_pathlessLayout.route-a.tsx` | `/route-a`                | `<Root><PathlessLayout><RouteA>`  |
| ʦ `_pathlessLayout.route-b.tsx` | `/route-b`                | `<Root><PathlessLayout><RouteB>`  |
| ʦ `files.$.tsx`                 | `/files/$`                | `<Root><Files>`                   |

## 混合使用扁平与目录路由

完全采用目录或扁平路由结构很可能不适合您的项目，因此 TanStack Router 允许混合使用两种方式，在适当场景下各取所长：

参考以下示例：

| 文件名                         | 路由路径                  | 组件输出                          |
| ------------------------------ | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`                 |                           | `<Root>`                          |
| ʦ `index.tsx`                  | `/` (精确匹配)            | `<Root><RootIndex>`               |
| ʦ `about.tsx`                  | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`                  | `/posts`                  | `<Root><Posts>`                   |
| 📂 `posts`                     |                           |                                   |
| ┄ ʦ `index.tsx`                | `/posts` (精确匹配)       | `<Root><Posts><PostsIndex>`       |
| ┄ ʦ `$postId.tsx`              | `/posts/$postId`          | `<Root><Posts><Post>`             |
| ┄ ʦ `$postId.edit.tsx`         | `/posts/$postId/edit`     | `<Root><Posts><Post><EditPost>`   |
| ʦ `settings.tsx`               | `/settings`               | `<Root><Settings>`                |
| ʦ `settings.profile.tsx`       | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ʦ `settings.notifications.tsx` | `/settings/notifications` | `<Root><Settings><Notifications>` |

扁平路由和目录路由可以混合使用，在适合的场景发挥各自优势。

> [!TIP]
> 如果默认的文件路由结构不符合您的需求，您可以使用[虚拟文件路由](./virtual-file-routes.md)来控制路由来源，同时仍能享受文件路由的强大性能优势。

## 开始使用文件路由

要开始使用文件路由，您需要配置项目的打包器以使用 TanStack Router 插件或 TanStack Router CLI。

启用文件路由需要 React 配合受支持的打包器使用。请查看以下配置指南中是否包含您使用的打包器：

[//]: # 'SupportedBundlersList'

- [Vite 安装指南](./installation-with-vite.md)
- [Rspack/Rsbuild 安装指南](./installation-with-rspack.md)
- [Webpack 安装指南](./installation-with-webpack.md)
- [Esbuild 安装指南](./installation-with-esbuild.md)

[//]: # 'SupportedBundlersList'

通过受支持的打包器使用 TanStack Router 的文件路由时，我们的插件会**在打包器的开发和构建流程中自动生成路由配置**。这是使用 TanStack Router 路由生成功能的最简单方式。

如果您的打包器尚未支持，可以通过 Discord 或 GitHub 联系我们。在此之前也无需担心！您仍然可以使用 [`@tanstack/router-cli`](./installation-with-router-cli.md) 包来生成路由树文件。
