---
source-updated-at: 2025-01-30T04:47:58.000Z
translation-updated-at: 2025-04-05T04:23:45.048Z
title: 比较
toc: false
---

在决定采用新工具之前，了解它与竞品的对比总是很有帮助！

> 本对比表格力求尽可能准确且公正。如果您使用过其中任何库并认为信息可以改进，欢迎通过页面底部的“在GitHub上编辑此页”链接提交修改建议（需附说明或证据支持）。

功能/能力说明：

- ✅ 原生支持，开箱即用，无需额外配置或代码
- 🔵 需通过附加包支持
- 🟡 部分支持（按5分制标注）
- 🔶 可实现，但需自定义代码/实现/类型转换
- 🛑 官方不支持

|                                     | TanStack Router / Start                          | React Router DOM [_(官网)_][router]            | Next.JS [_(官网)_][nextjs]                     |
| ----------------------------------- | ------------------------------------------------ | ---------------------------------------------- | ---------------------------------------------- |
| GitHub仓库/星标数                   | [![][stars-tanstack-router]][gh-tanstack-router] | [![][stars-router]][gh-router]                 | [![][stars-nextjs]][gh-nextjs]                 |
| 打包体积                            | [![][bp-tanstack-router]][bpl-tanstack-router]   | [![][bp-router]][bpl-router]                   | ❓                                             |
| History、Memory 和 Hash 路由        | ✅                                               | ✅                                             | 🛑                                             |
| 嵌套/布局路由                       | ✅                                               | ✅                                             | 🟡                                             |
| 类Suspense的路由过渡效果            | ✅                                               | ✅                                             | ✅                                             |
| 类型安全路由                        | ✅                                               | 🟡 (1/5)                                       | 🟡                                             |
| 基于代码的路由                      | ✅                                               | ✅                                             | 🛑                                             |
| 基于文件的路由                      | ✅                                               | ✅                                             | ✅                                             |
| 虚拟/编程式文件路由                 | ✅                                               | ✅                                             | 🛑                                             |
| 路由加载器                          | ✅                                               | ✅                                             | ✅                                             |
| SWR加载器缓存                       | ✅                                               | 🛑                                             | ✅                                             |
| 路由预加载                          | ✅                                               | ✅                                             | ✅                                             |
| 自动路由预加载                      | ✅                                               | ✅                                             | ✅                                             |
| 路由预加载延迟控制                  | ✅                                               | 🔶                                             | 🛑                                             |
| 路径参数                            | ✅                                               | ✅                                             | ✅                                             |
| 类型安全路径参数                    | ✅                                               | ✅                                             | 🛑                                             |
| 类型安全路由上下文                  | ✅                                               | 🛑                                             | 🛑                                             |
| 路径参数验证                        | ✅                                               | 🛑                                             | 🛑                                             |
| 自定义路径参数解析/序列化           | ✅                                               | 🛑                                             | 🛑                                             |
| 优先级路由                          | ✅                                               | ✅                                             | ✅                                             |
| 活动链接自定义                      | ✅                                               | ✅                                             | ✅                                             |
| 乐观UI更新                          | ✅                                               | ✅                                             | 🔶                                             |
| 类型安全的绝对/相对导航             | ✅                                               | 🛑                                             | 🛑                                             |
| 路由挂载/过渡/卸载事件              | ✅                                               | 🛑                                             | 🛑                                             |
| 开发工具                            | ✅                                               | 🛑                                             | 🛑                                             |
| 基础查询参数                        | ✅                                               | ✅                                             | ✅                                             |
| 查询参数钩子                        | ✅                                               | ✅                                             | ✅                                             |
| `<Link/>`/`useNavigate` 查询参数API | ✅                                               | 🟡 (仅支持通过`to`/`search`选项传递查询字符串) | 🟡 (仅支持通过`to`/`search`选项传递查询字符串) |
| JSON查询参数                        | ✅                                               | 🔶                                             | 🔶                                             |
| 类型安全查询参数                    | ✅                                               | 🛑                                             | 🛑                                             |
| 查询参数模式验证                    | ✅                                               | 🛑                                             | 🛑                                             |
| 查询参数不可变性+结构共享           | ✅                                               | 🔶                                             | 🛑                                             |
| 自定义查询参数解析/序列化           | ✅                                               | 🔶                                             | 🛑                                             |
| 查询参数中间件                      | ✅                                               | 🛑                                             | 🛑                                             |
| Suspense路由元素                    | ✅                                               | ✅                                             | ✅                                             |
| 路由错误元素                        | ✅                                               | ✅                                             | ✅                                             |
| 路由待定元素                        | ✅                                               | ✅                                             | ✅                                             |
| `<Block>`/`useBlocker`              | ✅                                               | 🔶                                             | ❓                                             |
| 延迟原语                            | ✅                                               | ✅                                             | ✅                                             |
| 导航滚动恢复                        | ✅                                               | ✅                                             | ❓                                             |
| 元素滚动恢复                        | ✅                                               | 🛑                                             | 🛑                                             |
| 异步滚动恢复                        | ✅                                               | 🛑                                             | 🛑                                             |
| 路由失效机制                        | ✅                                               | ✅                                             | ✅                                             |
| 运行时路由操纵（战争迷雾）          | 🛑                                               | ✅                                             | ✅                                             |
| --                                  | --                                               | --                                             | --                                             |
| **全栈能力**                        | --                                               | --                                             | --                                             |
| SSR                                 | ✅                                               | ✅                                             | ✅                                             |
| 流式SSR                             | ✅                                               | ✅                                             | ✅                                             |
| 通用RPC                             | ✅                                               | 🛑                                             | 🛑                                             |
| 通用RPC中间件                       | ✅                                               | 🛑                                             | 🛑                                             |
| React服务端函数                     | ✅                                               | 🛑                                             | ✅                                             |
| React服务端函数中间件               | ✅                                               | 🛑                                             | 🛑                                             |
| API路由                             | ✅                                               | ✅                                             | ✅                                             |
| API中间件                           | ✅                                               | 🛑                                             | ✅                                             |
| React服务端组件                     | 🛑                                               | 🛑                                             | ✅                                             |
| `<Form>` API                        | 🛑                                               | ✅                                             | ✅                                             |

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
