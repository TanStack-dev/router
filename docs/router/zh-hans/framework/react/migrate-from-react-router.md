---
source-updated-at: 2025-01-30T04:47:58.000Z
translation-updated-at: 2025-04-05T04:23:46.227Z
title: 从 React Router 迁移
toc: false
---

**_如果您的界面显示空白，请打开控制台，可能会看到类似 `cannot use 'useNavigate' outside of context` 的错误。这意味着仍有 React Router 的 API 被导入和引用，需要找到并移除。确保找到所有 React Router 导入的最简单方法是卸载 `react-router-dom`，然后您的文件中会出现 TypeScript 错误。这样您就知道需要将哪些内容更改为 `@tanstack/react-router` 的导入。_**

这里是 [示例仓库](https://github.com/Benanna2019/SickFitsForEveryone/tree/migrate-to-tanstack/router/React-Router)

- [ ] 安装 Router - `npm i @tanstack/react-router`
- [ ] **可选：** 卸载 React Router 以获取导入的 TypeScript 错误。
  - 目前尚不清楚是否可以逐步迁移，但似乎可以同时存在多个路由提供者，尽管这不理想。
  - React Router 和 TanStack Router 的 API 非常相似，如果您的公司采用这种方式，很可能在一两个冲刺周期内完成迁移。
- [ ] 为每个现有的 React Router 路由创建对应的路由
- [ ] 创建根路由
- [ ] 创建路由实例
- [ ] 在 main.tsx 中添加全局模块
- [ ] 从 main.tsx 中移除所有 React Router（`createBrowserRouter` 或 `BrowserRouter`）、`Routes` 和 `Route` 组件
- [ ] **可选：** 为自定义设置/提供者重构 `render` 函数 - 上述仓库中有示例 - 对于 Supertokens 来说这是必要的。Supertokens 在 React Router 中有特定设置，而在其他 React 实现中则不同
- [ ] 设置 RouterProvider 并将路由实例作为 prop 传递
- [ ] 将所有 React Router 的 `Link` 组件替换为 `@tanstack/react-router` 的 `Link` 组件
  - [ ] 添加 `to` prop 并指定字面路径
  - [ ] 在需要时添加 `params` prop，例如 `params={{ orderId: order.id }}`
- [ ] 将所有 React Router 的 `useNavigate` 钩子替换为 `@tanstack/react-router` 的 `useNavigate` 钩子
  - [ ] 根据需要设置 `to` 属性和 `params` 属性
- [ ] 将所有 React Router 的 `Outlet` 替换为 `@tanstack/react-router` 的等效组件
- [ ] 如果使用了 React Router 的 `useSearchParams` 钩子，将搜索参数的默认值移至路由定义的 `validateSearch` 属性
  - [ ] 使用 `@tanstack/react-router` 的 `Link` 组件的 `search` 属性来更新搜索参数状态，而不是使用 `useSearchParams` 钩子
  - [ ] 读取搜索参数可以像下面这样操作：
    - `const { page } = useSearch({ from: productPage.fullPath })`
- [ ] 如果使用了 React Router 的 `useParams` 钩子，将导入更新为 `@tanstack/react-router`，并设置 `from` 属性为您想要读取参数对象的字面路径名
  - 假设我们有一个路径名为 `orders/$orderid` 的路由。
  - 在 `useParams` 钩子中，我们会这样设置：`const params = useParams({ from: "/orders/$orderId" })`
  - 然后，在需要访问订单 ID 的地方，我们可以从参数对象中获取：`params.orderId`
