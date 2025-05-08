---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-05-08T20:16:17.792Z'
title: 路由匹配
---

# 路由匹配 (Route Matching)

路由匹配遵循一致且可預測的模式。本指南將解釋路由樹的匹配方式。

當 TanStack Router 處理您的路由樹時，所有路由會自動排序以優先匹配最具體的路由。這意味著無論路由樹的定義順序為何，路由始終會按照以下順序排序：

- 索引路由 (Index Route)
- 靜態路由 (Static Routes)（從最具體到最不具體）
- 動態路由 (Dynamic Routes)（從最長到最短）
- 萬用字元路由 (Splat/Wildcard Routes)

考慮以下虛擬路由樹：

```
Root
  - blog
    - $postId
    - /
    - new
  - /
  - *
  - about
  - about/us
```

排序後，此路由樹將變為：

```
Root
  - /
  - about/us
  - about
  - blog
    - /
    - new
    - $postId
  - *
```

這個最終順序代表路由將根據具體程度進行匹配的順序。

使用該路由樹，讓我們追蹤幾個不同 URL 的匹配過程：

- `/blog`
  ```
  Root
    ❌ /
    ❌ about/us
    ❌ about
    ⏩ blog
      ✅ /
      - new
      - $postId
    - *
  ```
- `/blog/my-post`
  ```
  Root
    ❌ /
    ❌ about/us
    ❌ about
    ⏩ blog
      ❌ /
      ❌ new
      ✅ $postId
    - *
  ```
- `/`
  ```
  Root
    ✅ /
    - about/us
    - about
    - blog
      - /
      - new
      - $postId
    - *
  ```
- `/not-a-route`
  ```
  Root
    ❌ /
    ❌ about/us
    ❌ about
    ❌ blog
      - /
      - new
      - $postId
    ✅ *
  ```
