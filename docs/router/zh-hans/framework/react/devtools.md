---
source-updated-at: 2025-03-08T12:20:26.000Z
translation-updated-at: 2025-04-05T04:23:45.117Z
title: 开发工具
---

> 链接，带上这把剑... 我是说开发工具！... 助你一路前行！

挥舞双手欢呼吧，因为 TanStack Router 配备了专属的开发工具！🥳

当你开始 TanStack Router 之旅时，这些开发工具将成为得力助手。它们能直观展示 TanStack Router 的内部运作机制，关键时刻可能为你节省数小时的调试时间！

## 安装

开发工具是独立包，需单独安装：

```sh
npm install -D @tanstack/react-router-devtools
```

或

```sh
pnpm add -D @tanstack/react-router-devtools
```

或

```sh
yarn add -D @tanstack/react-router-devtools
```

或

```sh
bun add -D @tanstack/react-router-devtools
```

## 导入开发工具

```js
import { TanStackRouterDevtools } from '@tanstack/react-router-devtools'
```

## 在生产环境使用开发工具

若以 `TanStackRouterDevtools` 导入，开发工具不会在生产环境显示。如需在 `process.env.NODE_ENV === 'production'` 环境下使用，请改用功能完全相同的 `TanStackRouterDevtoolsInProd`：

```tsx
import { TanStackRouterDevtoolsInProd } from '@tanstack/react-router-devtools'
```

## 在 `RouterProvider` 内部使用

最简单的使用方式是将开发工具渲染在根路由（或任意路由）中，这将自动连接到路由实例。

```tsx
const rootRoute = createRootRoute({
  component: () => (
    <>
      <Outlet />
      <TanStackRouterDevtools />
    </>
  ),
})

const routeTree = rootRoute.addChildren([
  // ... 其他路由
])

const router = createRouter({
  routeTree,
})

function App() {
  return <RouterProvider router={router} />
}
```

## 手动传递路由实例

如果不喜欢在 `RouterProvider` 内渲染开发工具，可以通过 `router` 属性直接传递路由实例。这样开发工具可以放置在页面任意位置：

```tsx
function App() {
  return (
    <>
      <RouterProvider router={router} />
      <TanStackRouterDevtools router={router} />
    </>
  )
}
```

## 浮动模式

浮动模式会将开发工具固定为悬浮元素，并在屏幕角落提供显示/隐藏开关。开关状态会通过 localStorage 持久化保存。

请将以下代码尽可能放在 React 应用的顶层，越靠近根节点效果越好：

```js
import { TanStackRouterDevtools } from '@tanstack/react-router-devtools'

function App() {
  return (
    <>
      <Router />
      <TanStackRouterDevtools initialIsOpen={false} />
    </>
  )
}
```

## 固定模式

要控制开发工具位置，可导入 `TanStackRouterDevtoolsPanel`：

```js
import { TanStackRouterDevtoolsPanel } from '@tanstack/react-router-devtools'
```

然后将其附加到指定的 Shadow DOM 目标：

```js
<TanStackRouterDevtoolsPanel
  shadowDOMTarget={shadowContainer}
  router={router}
/>
```

点击[此处](https://tanstack.com/router/latest/docs/framework/react/examples/basic-devtools-panel)查看 StackBlitz 上的实时示例。

### 配置项

- `router: Router`
  - 要连接的路由实例
- `initialIsOpen: Boolean`
  - 设为 `true` 可使开发工具默认展开
- `panelProps: PropsObject`
  - 用于向面板添加属性，例如 `className`、`style`（会合并覆盖默认样式）等
- `closeButtonProps: PropsObject`
  - 用于向关闭按钮添加属性，例如 `className`、`style`（会合并覆盖默认样式）、`onClick`（可扩展默认处理器）等
- `toggleButtonProps: PropsObject`
  - 用于向切换按钮添加属性，例如 `className`、`style`（会合并覆盖默认样式）、`onClick`（可扩展默认处理器）等
- `position?: "top-left" | "top-right" | "bottom-left" | "bottom-right"`
  - 默认为 `bottom-left`
  - 控制开发工具面板开关的 TanStack Router 徽标位置
- `shadowDOMTarget?: ShadowRoot`
  - 指定开发工具的 Shadow DOM 目标
  - 默认情况下样式会应用到主文档的 `<head>` 标签（light DOM），指定后样式将应用到此 Shadow DOM 内

## 嵌入式模式

嵌入式模式会将开发工具作为常规组件嵌入应用，之后可自由定制样式：

```js
import { TanStackRouterDevtoolsPanel } from '@tanstack/react-router-devtools'

function App() {
  return (
    <>
      <Router router={router} />
      <TanStackRouterDevtoolsPanel
        router={router}
        style={styles}
        className={className}
      />
    </>
  )
}
```

### 配置项

使用以下选项定制开发工具样式：

- `style: StyleObject`
  - 标准的 React 样式对象，用于内联样式
- `className: string`
  - 标准的 React className 属性，用于类名样式
