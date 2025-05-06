---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:15:23.000Z
title: 路由树
---

TanStack Router 采用嵌套路由树结构，将 URL 与需要渲染的正确组件树进行匹配。

构建路由树时，TanStack Router 支持两种方式：

- 基于文件的路由
- 基于代码的路由

这两种方法支持完全相同的核心特性和功能，但**基于文件的路由能以更少的代码实现相同或更好的效果**。因此，**基于文件的路由是 TanStack Router 推荐的首选配置方式**，且大部分文档内容都从基于文件路由的角度进行编写。

如需了解基于代码的路由文档，请参阅[《基于代码的路由》](./code-based-routing.md)指南。

## 路由树

嵌套路由是一个强大的概念，允许通过 URL 渲染嵌套组件树。例如，对于 `/blog/posts/123` 这个 URL，可以创建如下路由层次结构：

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

让我们将这个概念扩展到一个更复杂的站点结构，现在用文件名表示：

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

以上是 TanStack Router 可用的有效路由树配置！基于文件的路由蕴含了大量强大约定，下面我们来逐步解析。

## 路由树配置

路由树可以通过以下几种方式配置：

- [扁平路由](./file-based-routing.md#flat-routes)
- [目录路由](./file-based-routing.md#directory-routes)
- [扁平与目录混合路由](./file-based-routing.md#mixed-flat-and-directory-routes)
- [虚拟文件路由](./virtual-file-routes.md)
- [基于代码的路由](./code-based-routing.md)

请查阅上方完整文档链接了解每种路由树类型，或直接进入下一章节开始基于文件的路由配置。
