---
source-updated-at: '2025-04-15T21:31:52.000Z'
translation-updated-at: '2025-05-06T22:02:08.525Z'
title: 比较
toc: false
---

在决定采用新工具之前，了解它与竞品的对比总是很有帮助的！

> 本对比表力求准确且公正。如果您使用过这些库并认为信息可以改进，欢迎通过页面底部的 "Edit this page on GitHub" 链接提交修改建议（需附说明或证据）。

功能/能力说明：

- ✅ 原生支持，开箱即用，无需额外配置或代码
- 🔵 需通过扩展包支持
- 🟡 部分支持（按 5 分制标注）
- 🔶 可实现，但需自定义代码/实现/类型转换
- 🛑 官方不支持

|                                      | TanStack Router / Start                          | React Router DOM [_(官网)_][router]          | Next.JS [_(官网)_][nextjs]                   |
| ------------------------------------ | ------------------------------------------------ | -------------------------------------------- | -------------------------------------------- |
| GitHub 仓库 / Star 数                | [![][stars-tanstack-router]][gh-tanstack-router] | [![][stars-router]][gh-router]               | [![][stars-nextjs]][gh-nextjs]               |
| 打包体积                             | [![][bp-tanstack-router]][bpl-tanstack-router]   | [![][bp-router]][bpl-router]                 | ❓                                           |
| 历史路由、内存路由与哈希路由         | ✅                                               | ✅                                           | 🛑                                           |
| 嵌套/布局路由                        | ✅                                               | ✅                                           | 🟡                                           |
| 类 Suspense 的路由过渡效果           | ✅                                               | ✅                                           | ✅                                           |
| 类型安全路由                         | ✅                                               | 🟡 (1/5)                                     | 🟡                                           |
| 基于代码的路由                       | ✅                                               | ✅                                           | 🛑                                           |
| 基于文件的路由                       | ✅                                               | ✅                                           | ✅                                           |
| 虚拟/编程式文件路由                  | ✅                                               | ✅                                           | 🛑                                           |
| 路由加载器                           | ✅                                               | ✅                                           | ✅                                           |
| SWR 加载器缓存                       | ✅                                               | 🛑                                           | ✅                                           |
| 路由预加载                           | ✅                                               | ✅                                           | ✅                                           |
| 自动路由预加载                       | ✅                                               | ✅                                           | ✅                                           |
| 路由预加载延迟                       | ✅                                               | 🔶                                           | 🛑                                           |
| 路径参数                             | ✅                                               | ✅                                           | ✅                                           |
| 类型安全路径参数                     | ✅                                               | ✅                                           | 🛑                                           |
| 类型安全路由上下文                   | ✅                                               | 🛑                                           | 🛑                                           |
| 路径参数验证                         | ✅                                               | 🛑                                           | 🛑                                           |
| 自定义路径参数解析/序列化            | ✅                                               | 🛑                                           | 🛑                                           |
| 优先级路由                           | ✅                                               | ✅                                           | ✅                                           |
| 活动链接定制                         | ✅                                               | ✅                                           | ✅                                           |
| 乐观更新 UI                          | ✅                                               | ✅                                           | 🔶                                           |
| 类型安全的绝对/相对导航              | ✅                                               | 🛑                                           | 🛑                                           |
| 路由挂载/过渡/卸载事件               | ✅                                               | 🛑                                           | 🛑                                           |
| 开发者工具                           | ✅                                               | 🛑                                           | 🛑                                           |
| 基础查询参数                         | ✅                                               | ✅                                           | ✅                                           |
| 查询参数钩子                         | ✅                                               | ✅                                           | ✅                                           |
| `<Link/>`/`useNavigate` 查询参数 API | ✅                                               | 🟡 (仅支持通过 `to`/`search` 选项传递字符串) | 🟡 (仅支持通过 `to`/`search` 选项传递字符串) |
| JSON 查询参数                        | ✅                                               | 🔶                                           | 🔶                                           |
| 类型安全查询参数                     | ✅                                               | 🛑                                           | 🛑                                           |
| 查询参数模式验证                     | ✅                                               | 🛑                                           | 🛑                                           |
| 查询参数不可变性 + 结构共享          | ✅                                               | 🔶                                           | 🛑                                           |
| 自定义查询参数解析/序列化            | ✅                                               | 🔶                                           | 🛑                                           |
| 查询参数中间件                       | ✅                                               | 🛑                                           | 🛑                                           |
| Suspense 路由元素                    | ✅                                               | ✅                                           | ✅                                           |
| 路由错误边界元素                     | ✅                                               | ✅                                           | ✅                                           |
| 路由加载中元素                       | ✅                                               | ✅                                           | ✅                                           |
| `<Block>`/`useBlocker`               | ✅                                               | 🔶                                           | ❓                                           |
| 延迟渲染原语                         | ✅                                               | ✅                                           | ✅                                           |
| 导航滚动恢复                         | ✅                                               | ✅                                           | ❓                                           |
| 元素级滚动恢复                       | ✅                                               | 🛑                                           | 🛑                                           |
| 异步滚动恢复                         | ✅                                               | 🛑                                           | 🛑                                           |
| 路由失效机制                         | ✅                                               | ✅                                           | ✅                                           |
| 运行时路由操控（战争迷雾）           | 🛑                                               | ✅                                           | ✅                                           |
| 并行路由                             | 🛑                                               | 🛑                                           | ✅                                           |
| --                                   | --                                               | --                                           | --                                           |
| **全栈能力**                         | --                                               | --                                           | --                                           |
| 服务端渲染 (SSR)                     | ✅                                               | ✅                                           | ✅                                           |
| 流式服务端渲染 (Streaming SSR)       | ✅                                               | ✅                                           | ✅                                           |
| 通用 RPC                             | ✅                                               | 🛑                                           | 🛑                                           |
| 通用 RPC 中间件                      | ✅                                               | 🛑                                           | 🛑                                           |
| React 服务端函数                     | ✅                                               | 🛑                                           | ✅                                           |
| React 服务端函数中间件               | ✅                                               | 🛑                                           | 🛑                                           |
| API 路由                             | ✅                                               | ✅                                           | ✅                                           |
| API 中间件                           | ✅                                               | 🛑                                           | ✅                                           |
| React 服务端组件 (RSC)               | 🛑                                               | 🛑                                           | ✅                                           |
| `<Form>` API                         | 🛑                                               | ✅                                           | ✅                                           |

[bp-tanstack-router]: https://badgen.net/bundlephobia/minzip/@tanstack/react-router
[bpl-tanstack-router]: https://bundlephobia.com/result?p=@tanstack/react-router
[gh-tanstack-router]: https://github.com/tanstack/router
[stars-tanstack-router]: https://img.shields.io/github/stars/tanstack/router?label=%F0%9F%8C%9F
[_]: _
[router]: https://github.com/remix-run/react-router
[bp-router]: https://badgen.net/bundlephobia/minzip/react-router
[gh-router]: https://github.com/remix-run/react-router
[stars-router]: https://img.shields.io/github/stars/remix-run/react-router?label=%F0%9F%8C%9F
[bpl-router]: https://bundlephobia.com/result?p=react-router
[bpl-history]: https://bundlephobia.com/result?p=history
[_]: _
[nextjs]: https://nextjs.org/docs/routing/introduction
[bp-nextjs]: https://badgen.net/bundlephobia/minzip/next.js?label=All
[gh-nextjs]: https://github.com/vercel/next.js
[stars-nextjs]: https://img.shields.io/github/stars/vercel/next.js?label=%F0%9F%8C%9F
[bpl-nextjs]: https://bundlephobia.com/result?p=next
