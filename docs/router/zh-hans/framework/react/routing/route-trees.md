---
source-updated-at: '2025-05-05T22:38:59.000Z'
translation-updated-at: '2025-05-06T22:16:55.776Z'
title: 路由树
---

TanStack Router 采用嵌套路由树 (Route Tree) 结构，将 URL 与需要渲染的正确组件树进行匹配。

构建路由树时，TanStack Router 支持以下两种方式：

- [基于文件的路由 (File-Based Routing)](./file-based-routing.md)
- [基于代码的路由 (Code-Based Routing)](./code-based-routing.md)

两种方式支持完全相同的核心功能，但**基于文件的路由能以更少的代码实现相同或更好的效果**。因此，**我们推荐并优先使用基于文件的路由**来配置 TanStack Router。文档大部分内容都是从基于文件路由的角度编写的。

## 路由树 (Route Trees)

嵌套路由 (Nested Routing) 是一种强大的概念，允许通过 URL 渲染嵌套的组件树。例如，对于 URL `/blog/posts/123`，可以创建如下路由层级结构：

```tsx
├── blog
│   ├── posts
│   │   ├── $postId
```

并渲染出如下组件树：

```tsx
<Blog>
  <Posts>
    <Post postId="123" />
  </Posts>
</Blog>
```

让我们将这个概念扩展到一个更复杂的站点结构，这次用文件名表示：

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

以上是 TanStack Router 可用的有效路由树配置！基于文件的路由蕴含了许多强大约定，下面我们将逐步解析。

## 路由树配置 (Route Tree Configuration)

路由树可以通过以下几种方式配置：

- [扁平路由 (Flat Routes)](./file-based-routing.md#flat-routes)
- [目录路由 (Directories)](./file-based-routing.md#directory-routes)
- [混合扁平与目录路由 (Mixed Flat Routes and Directories)](./file-based-routing.md#mixed-flat-and-directory-routes)
- [虚拟文件路由 (Virtual File Routes)](./virtual-file-routes.md)
- [基于代码的路由 (Code-Based Routes)](./code-based-routing.md)

建议查阅上述每种路由树类型的完整文档链接，或直接进入下一章节开始基于文件的路由学习。
