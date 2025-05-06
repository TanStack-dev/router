---
source-updated-at: 2025-02-25T23:33:22.000Z
translation-updated-at: 2025-04-05T03:15:51.000Z
title: 路由匹配
---

路由匹配遵循一致且可预测的模式。本指南将解释路由树是如何进行匹配的。

当 TanStack Router 处理您的路由树时，所有路由都会自动排序，优先匹配最具体的路由。这意味着无论您定义路由树的顺序如何，路由始终会按照以下顺序排序：

- 索引路由
- 静态路由（从最具体到最不具体）
- 动态路由（从最长到最短）
- 通配符路由

考虑以下伪路由树：

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

排序后，该路由树将变为：

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

这个最终顺序代表了路由将根据具体性进行匹配的顺序。

使用该路由树，让我们跟踪几个不同 URL 的匹配过程：

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
