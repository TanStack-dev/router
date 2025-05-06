---
source-updated-at: 2025-03-29T11:29:39.000Z
translation-updated-at: 2025-04-05T03:40:13.000Z
title: 快速开始
id: quick-start
---

## 没耐心？

如果你迫不及待想开始，可以直接克隆并运行[基础示例](../examples/start-basic)，执行以下命令：

```bash
npx gitpick TanStack/router/tree/main/examples/react/start-basic start-basic
cd start-basic
npm install
npm run dev
```

如果想使用其他示例，只需将上述命令中的 `start-basic` 替换为下方列表中你感兴趣的示例标识符。

克隆完目标示例后，返回[基础教程](../learn-the-basics)学习如何使用 TanStack Start！

## 示例项目

TanStack Start 准备了丰富的示例项目助你快速上手。从以下选择一个开始探索吧！

- [基础版](../examples/start-basic) (start-basic)
- [基础版+鉴权](../examples/start-basic-auth) (start-basic-auth)
- [计数器](../examples/start-counter) (start-counter)
- [基础版+React Query](../examples/start-basic-react-query) (start-basic-react-query)
- [Clerk鉴权](../examples/start-clerk-basic) (start-clerk-basic)
- [Convex+Trellaux](../examples/start-convex-trellaux) (start-convex-trellaux)
- [Supabase](../examples/start-supabase-basic) (start-supabase-basic)
- [Trellaux](../examples/start-trellaux) (start-trellaux)
- [Material UI](../examples/start-material-ui) (start-material-ui)

### Stackblitz 预览

每个示例都内置了 Stackblitz 预览功能，方便你快速找到合适的起点

### 快速部署

点击示例页面上的 **Deploy to Netlify** 按钮，即可将示例克隆并一键部署到 Netlify。

### 手动部署

如需手动克隆并部署到其他平台，请执行以下命令（将 `EXAMPLE_SLUG` 替换为上方示例的标识符）：

```bash
npx gitpick TanStack/router/tree/main/examples/react/EXAMPLE_SLUG my-new-project
cd my-new-project
npm install
npm run dev
```

完成克隆或部署后，记得返回[基础教程](../learn-the-basics)学习 TanStack Start 的使用方法！

## 其他路由示例

虽然不是 Start 专属示例，但这些项目能帮助你深入理解 TanStack Router 的工作原理：

- [快速开始（基于文件）](../examples/quickstart-file-based)
- [基础版（基于文件）](../examples/basic-file-based)
- [功能大全（基于文件）](../examples/kitchen-sink-file-based)
- [功能大全+React Query（基于文件）](../examples/kitchen-sink-react-query-file-based)
- [路径伪装](../examples/location-masking)
- [鉴权路由](../examples/authenticated-routes)
- [滚动恢复](../examples/scroll-restoration)
- [延迟加载](../examples/deferred-data)
- [导航拦截](../examples/navigation-blocking)
- [视图过渡](../examples/view-transitions)
- [结合 tRPC](../examples/with-trpc)
- [结合 tRPC+React Query](../examples/with-trpc-react-query)
