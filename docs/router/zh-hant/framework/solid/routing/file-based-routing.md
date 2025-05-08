---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-05-08T20:16:55.692Z'
title: 基於檔案的路由
---

大多數 TanStack Router 的文件都是針對基於檔案的 (file-based) 路由編寫的，旨在幫助你更詳細地了解如何配置基於檔案的路由及其背後的技術細節。雖然基於檔案的路由是配置 TanStack Router 的首選和推薦方式，但如果你偏好，也可以使用 [基於程式碼的路由 (code-based routing)](./code-based-routing.md)。

## 什麼是基於檔案的路由 (File-Based Routing)？

基於檔案的路由是一種利用檔案系統來配置路由的方式。與通過程式碼定義路由結構不同，你可以使用一系列檔案和目錄來表示應用程式的路由層級結構。這種方式帶來許多好處：

- **簡潔性**：基於檔案的路由在視覺上直觀，無論對新手還是資深開發者都易於理解。
- **組織性**：路由的組織方式與應用程式的 URL 結構一致。
- **擴展性**：隨著應用程式的增長，基於檔案的路由讓新增和維護路由變得更加容易。
- **程式碼分割 (Code-Splitting)**：基於檔案的路由讓 TanStack Router 能自動進行程式碼分割，提升效能。
- **型別安全 (Type-Safety)**：基於檔案的路由提高了型別安全性的上限，自動生成和管理路由的型別連結，而這在基於程式碼的路由中可能是一個繁瑣的過程。
- **一致性**：基於檔案的路由強制執行一致的路由結構，使維護和更新應用程式以及在不同專案間切換變得更加容易。

## 使用 `/` 還是 `.`？

雖然目錄長期以來被用來表示路由層級，但基於檔案的路由引入了使用檔案名稱中的 `.` 字符來表示路由嵌套的概念。這讓你可以避免為少數深度嵌套的路由創建目錄，同時繼續使用目錄來組織更廣泛的路由層級。讓我們來看一些例子！

## 目錄路由 (Directory Routes)

目錄可以用來表示路由層級，這對於將多個路由組織成邏輯群組以及減少深度嵌套路由的檔案名稱長度非常有用。

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

## 扁平路由 (Flat Routes)

扁平路由讓你可以使用 `.` 來表示路由嵌套層級。

這在你有大量獨特的深度嵌套路由且希望避免為每個路由創建目錄時非常有用：

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

## 混合扁平與目錄路由 (Mixed Flat and Directory Routes)

在實際專案中，100% 使用目錄或扁平路由結構可能並非最佳選擇，因此 TanStack Router 允許你混合使用扁平路由和目錄路由，以在適當的地方結合兩者的優勢：

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

扁平路由和目錄路由可以混合使用，以在適當的地方結合兩者的優勢。

> [!TIP]
> 如果你發現預設的基於檔案的路由結構不符合你的需求，隨時可以使用 [虛擬檔案路由 (Virtual File Routes)](./virtual-file-routes.md) 來控制路由的來源，同時仍能享受基於檔案路由的卓越效能優勢。

## 開始使用基於檔案的路由 (Getting started with File-Based Routing)

要開始使用基於檔案的路由，你需要配置專案的打包工具 (bundler) 以使用 TanStack Router 插件或 TanStack Router CLI。

要啟用基於檔案的路由，你需要使用 React 並搭配支援的打包工具。請查看以下配置指南中是否有你的打包工具：

- [使用 Vite 配置](#configuration-with-vite)

當通過其中一個支援的打包工具使用 TanStack Router 的基於檔案的路由時，我們的插件會 **通過打包工具的開發和構建流程自動生成你的路由配置**。這是使用 TanStack Router 路由生成功能的最簡單方式。

如果你的打包工具尚未支援，可以在 Discord 或 GitHub 上聯繫我們。在此之前，別擔心！你仍然可以使用 [`@tanstack/router-cli`](./installation-with-router-cli.md) 套件來生成你的路由樹檔案。
