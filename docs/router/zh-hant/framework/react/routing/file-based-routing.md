---
source-updated-at: '2025-03-28T17:52:54.000Z'
translation-updated-at: '2025-05-08T20:18:10.162Z'
title: 基於檔案的路由
---

大多數 TanStack Router 的文件都是針對基於檔案的路由 (file-based routing) 所撰寫，旨在幫助您更詳細地了解如何配置基於檔案的路由及其背後的技術細節。雖然基於檔案的路由是配置 TanStack Router 的首選和推薦方式，但如果您偏好，也可以使用[基於程式碼的路由 (code-based routing)](./code-based-routing.md)。

## 什麼是基於檔案的路由？

基於檔案的路由是一種利用檔案系統來配置路由的方式。您無需透過程式碼定義路由結構，而是使用一系列檔案和目錄來代表應用程式的路由層次結構。這種方式帶來許多好處：

- **簡潔性**：基於檔案的路由在視覺上直觀，無論對新手或資深開發者都易於理解。
- **組織性**：路由的組織方式反映了應用程式的 URL 結構。
- **擴展性**：隨著應用程式的增長，基於檔案的路由讓新增和維護路由變得更加容易。
- **程式碼分割 (Code-Splitting)**：基於檔案的路由允許 TanStack Router 自動進行路由的程式碼分割，從而提升效能。
- **型別安全 (Type-Safety)**：基於檔案的路由透過生成和管理路由的型別連結，提高了型別安全性的上限，這在基於程式碼的路由中可能是一個繁瑣的過程。
- **一致性**：基於檔案的路由強制執行一致的路由結構，使應用程式的維護和更新更加容易，也便於在不同專案之間切換。

## 使用 `/` 還是 `.`？

雖然目錄長期以來被用來表示路由的層次結構，但基於檔案的路由引入了在檔案名稱中使用 `.` 字符來表示路由嵌套的概念。這讓您可以避免為少數深度嵌套的路由創建目錄，同時繼續在更廣泛的路由層次結構中使用目錄。讓我們來看一些例子！

## 目錄路由

目錄可用於表示路由層次結構，這對於將多個路由組織成邏輯群組非常有用，同時也能減少深度嵌套路由的檔案名稱長度。

請看以下範例：

| 檔案名稱                | 路由路徑                  | 元件輸出                          |
| ----------------------- | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`          |                           | `<Root>`                          |
| ʦ `index.tsx`           | `/` (exact)               | `<Root><RootIndex>`               |
| ʦ `about.tsx`           | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`           | `/posts`                  | `<Root><Posts>`                   |
| 📂 `posts`              |                           |                                   |
| ┄ ʦ `index.tsx`         | `/posts` (exact)          | `<Root><Posts><PostsIndex>`       |
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

扁平路由讓您能夠使用 `.` 來表示路由的嵌套層級。

當您有大量獨特的深度嵌套路由，並希望避免為每個路由創建目錄時，這會非常有用：

請看以下範例：

| 檔案名稱                        | 路由路徑                  | 元件輸出                          |
| ------------------------------- | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`                  |                           | `<Root>`                          |
| ʦ `index.tsx`                   | `/` (exact)               | `<Root><RootIndex>`               |
| ʦ `about.tsx`                   | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`                   | `/posts`                  | `<Root><Posts>`                   |
| ʦ `posts.index.tsx`             | `/posts` (exact)          | `<Root><Posts><PostsIndex>`       |
| ʦ `posts.$postId.tsx`           | `/posts/$postId`          | `<Root><Posts><Post>`             |
| ʦ `posts_.$postId.edit.tsx`     | `/posts/$postId/edit`     | `<Root><EditPost>`                |
| ʦ `settings.tsx`                | `/settings`               | `<Root><Settings>`                |
| ʦ `settings.profile.tsx`        | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ʦ `settings.notifications.tsx`  | `/settings/notifications` | `<Root><Settings><Notifications>` |
| ʦ `_pathlessLayout.tsx`         |                           | `<Root><PathlessLayout>`          |
| ʦ `_pathlessLayout.route-a.tsx` | `/route-a`                | `<Root><PathlessLayout><RouteA>`  |
| ʦ `_pathlessLayout.route-b.tsx` | `/route-b`                | `<Root><PathlessLayout><RouteB>`  |
| ʦ `files.$.tsx`                 | `/files/$`                | `<Root><Files>`                   |

## 混合扁平與目錄路由

完全使用目錄或扁平路由結構很可能不適合您的專案，因此 TanStack Router 允許您將扁平路由和目錄路由混合使用，以創建一個在適當情況下結合兩者優點的路由樹：

請看以下範例：

| 檔案名稱                       | 路由路徑                  | 元件輸出                          |
| ------------------------------ | ------------------------- | --------------------------------- |
| ʦ `__root.tsx`                 |                           | `<Root>`                          |
| ʦ `index.tsx`                  | `/` (exact)               | `<Root><RootIndex>`               |
| ʦ `about.tsx`                  | `/about`                  | `<Root><About>`                   |
| ʦ `posts.tsx`                  | `/posts`                  | `<Root><Posts>`                   |
| 📂 `posts`                     |                           |                                   |
| ┄ ʦ `index.tsx`                | `/posts` (exact)          | `<Root><Posts><PostsIndex>`       |
| ┄ ʦ `$postId.tsx`              | `/posts/$postId`          | `<Root><Posts><Post>`             |
| ┄ ʦ `$postId.edit.tsx`         | `/posts/$postId/edit`     | `<Root><Posts><Post><EditPost>`   |
| ʦ `settings.tsx`               | `/settings`               | `<Root><Settings>`                |
| ʦ `settings.profile.tsx`       | `/settings/profile`       | `<Root><Settings><Profile>`       |
| ʦ `settings.notifications.tsx` | `/settings/notifications` | `<Root><Settings><Notifications>` |

扁平路由和目錄路由可以混合使用，以創建一個在適當情況下結合兩者優點的路由樹。

> [!TIP]
> 如果您發現預設的基於檔案的路由結構不符合您的需求，您隨時可以使用[虛擬檔案路由 (Virtual File Routes)](./virtual-file-routes.md) 來控制路由的來源，同時仍能享受基於檔案路由的卓越效能優勢。

## 開始使用基於檔案的路由

要開始使用基於檔案的路由，您需要配置專案的打包工具 (bundler) 以使用 TanStack Router 插件或 TanStack Router CLI。

要啟用基於檔案的路由，您需要使用 React 並搭配支援的打包工具。請查看以下配置指南，確認您的打包工具是否在支援列表中。

[//]: # 'SupportedBundlersList'

- [使用 Vite 安裝](./installation-with-vite.md)
- [使用 Rspack/Rsbuild 安裝](./installation-with-rspack.md)
- [使用 Webpack 安裝](./installation-with-webpack.md)
- [使用 Esbuild 安裝](./installation-with-esbuild.md)

[//]: # 'SupportedBundlersList'

當透過其中一個支援的打包工具使用 TanStack Router 的基於檔案的路由時，我們的插件將**透過打包工具的開發和建置流程自動生成您的路由配置**。這是使用 TanStack Router 路由生成功能的最簡單方式。

如果您的打包工具尚未支援，您可以在 Discord 或 GitHub 上聯繫我們。在此之前，請別擔心！您仍然可以使用 [`@tanstack/router-cli`](./installation-with-router-cli.md) 套件來生成您的路由樹檔案。
