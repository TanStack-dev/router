---
source-updated-at: '2025-05-05T22:38:59.000Z'
translation-updated-at: '2025-05-08T20:16:16.080Z'
title: 路由樹
---

TanStack Router 使用嵌套的路由樹 (Route Tree) 來匹配 URL 並渲染正確的元件樹 (Component Tree)。

要建立路由樹，TanStack Router 支援以下方式：

- [檔案式路由 (File-Based Routing)](./file-based-routing.md)
- [程式碼式路由 (Code-Based Routing)](./code-based-routing.md)

兩種方法都支援完全相同的核心功能，但**檔案式路由能用更少的程式碼達到相同或更好的效果**。因此，**檔案式路由是我們推薦的首選方式**。大部分文件內容都會以檔案式路由的角度撰寫。

## 路由樹 (Route Trees)

嵌套路由 (Nested Routing) 是一個強大的概念，可讓你透過 URL 渲染嵌套的元件樹。例如，給定 URL `/blog/posts/123`，你可以建立如下的路由層級結構：

```tsx
├── blog
│   ├── posts
│   │   ├── $postId
```

並渲染出如下的元件樹：

```tsx
<Blog>
  <Posts>
    <Post postId="123" />
  </Posts>
</Blog>
```

讓我們將這個概念擴展到更大的網站結構，這次改用檔案名稱表示：

```
/routes
├── __root.tsx
├── index.tsx
├── about.tsx
├── posts/
│   ├── index.tsx
│   ├── $postId.tsx
├── posts.$postId.edit.tsx
├── settings/
│   ├── profile.tsx
│   ├── notifications.tsx
├── _pathlessLayout/
│   ├── route-a.tsx
├── ├── route-b.tsx
├── files/
│   ├── $.tsx
```

以上是一個有效的路由樹配置，可用於 TanStack Router！檔案式路由蘊含許多強大的約定，讓我們進一步解析。

## 路由樹配置 (Route Tree Configuration)

路由樹可以透過以下幾種方式配置：

- [扁平路由 (Flat Routes)](./file-based-routing.md#flat-routes)
- [目錄結構路由 (Directories)](./file-based-routing.md#directory-routes)
- [混合扁平與目錄路由 (Mixed Flat Routes and Directories)](./file-based-routing.md#mixed-flat-and-directory-routes)
- [虛擬檔案路由 (Virtual File Routes)](./virtual-file-routes.md)
- [程式碼式路由 (Code-Based Routes)](./code-based-routing.md)

請務必查閱上述每種路由樹類型的完整文件連結，或直接繼續下一節開始使用檔案式路由。
