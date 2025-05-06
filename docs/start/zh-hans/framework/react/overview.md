---
source-updated-at: '2025-05-05T22:15:09.000Z'
translation-updated-at: '2025-05-06T22:19:50.505Z'
id: overview
title: 概述
---

TanStack Start 是一个基于 TanStack Router 构建的全栈 React 框架。它通过 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 等工具提供完整的文档级服务端渲染 (SSR)、流式传输、服务端函数、打包等功能，并支持一键部署到您偏好的托管平台！

## 选择 Router 还是 Start？

TanStack Router 是一个强大、类型安全且功能丰富的 React 应用路由系统，专为轻松应对最复杂的全栈路由需求而设计。TanStack Start 在 Router 的类型系统基础上，进一步提供了类型安全的全栈 API，助您保持高效开发。

TanStack Router 提供以下功能：

- 100% 类型推断的 TypeScript 支持
- 类型安全的导航
- 嵌套路由和无路径布局路由
- 内置路由加载器及 SWR 缓存
- 专为客户端数据缓存设计（兼容 TanStack Query、SWR 等）
- 自动路由预加载
- 异步路由组件与错误边界
- 基于文件的路由生成
- 类型安全的 JSON 优先搜索参数状态管理 API
- 路径与搜索参数的模式验证
- 搜索参数导航 API
- 自定义搜索参数解析器/序列化器支持
- 搜索参数中间件
- 路由匹配/加载中间件

TanStack Start 提供以下功能：

- 完整的文档级服务端渲染 (SSR)
- 流式传输
- 服务端函数 / 远程过程调用 (RPC)
- 代码打包
- 部署支持
- 全栈类型安全

**总结：使用 TanStack Router 处理客户端路由，使用 TanStack Start 处理全栈路由。**

## 工作原理

TanStack Start 使用 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 来打包和部署您的应用。实际上，这些工具也正是 Solid Start 的核心！通过这些工具，我们实现了以往无法实现的功能：

- 为服务端渲染 (SSR)、流式传输和注水 (hydration) 提供统一 API
- 从客户端代码中分离仅限服务端的代码（例如服务端函数）
- 为应用打包以部署到任意托管平台

## 适用场景

如果您需要构建具备以下需求的全栈 React 应用，TanStack Start 将是理想选择：

- 完整的文档级服务端渲染 (SSR) 与注水 (hydration)
- 流式传输
- 服务端函数 / 远程过程调用 (RPC)
- 全栈类型安全
- 健壮的路由系统
- 丰富的客户端交互能力

## 不适用场景

以下情况不建议使用 TanStack Start：

- 纯静态网站
- 目标是无 JavaScript 或仅有极简客户端交互的服务端渲染网站
- 需要 React 服务端组件 (RSC) 优先的框架（我们将很快推出独具特色的 RSC 支持！）

## 资金支持

TanStack 与合作伙伴紧密协作，在提供最佳开发者体验的同时，确保解决方案具备普适性并经过行业专家验证。每位合作伙伴在 TanStack 生态中扮演独特角色：

- **Clerk**  
  <a href="https://go.clerk.com/wOwHtuJ" alt="Clerk Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" style="height: 40px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">  
  <img alt="Clerk logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">  
  </picture>  
  </a>  
  为现代 Web 应用（包括 TanStack Start 应用）提供最佳认证体验。Clerk 为 TanStack Start 用户提供一流的认证集成方案，并与 TanStack 团队密切合作，确保框架 API 符合最新认证实践标准。

- **Netlify**  
  <a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" style="height: 90px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">  
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">  
  </picture>  
  </a>  
  领先的 Web 应用托管平台，提供快速、安全、可靠的部署环境。我们与 Netlify 深度合作，确保 TanStack Start 应用不仅能无缝部署至其平台，还能实现性能、安全与可靠性的最佳实践——无论最终部署到哪里。

- **Neon**  
  <a href="https://neon.tech?utm_source=tanstack" alt="Neon Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-dark.svg" style="height: 60px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" style="height: 60px;">  
  <img alt="Neon logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" style="height: 60px;">  
  </picture>  
  </a>  
  专为现代全栈应用打造的无服务器、自动扩展的 Postgres 解决方案。Neon 与 TanStack Start 深度集成，支持服务端函数和数据库驱动的路由。我们共同为开发者简化数据库体验。

- **Convex**  
  <a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" style="height: 40px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">  
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">  
  </picture>  
  </a>  
  与 TanStack Start 无缝集成的无服务器数据库平台。Convex 简化了应用数据管理流程，提供实时、可扩展且支持事务的数据后端，完美适配 TanStack Start 应用。Convex 还与 TanStack 团队紧密协作，确保框架 API 符合最新数据库实践标准。

- **Sentry**  
  <a href="https://sentry.io?utm_source=tanstack" alt='Sentry Logo'>  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-dark.svg" style="height: 60px;">  
  <img alt="Sentry logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">  
  </picture>  
  </a>  
  功能强大的可观测性平台，与 TanStack Start 无缝集成。Sentry 帮助开发者实时监控并修复崩溃问题，提供应用性能与错误追踪的深度洞察。Sentry 与 TanStack 团队密切合作，确保框架 API 符合最新可观测性实践标准。

## 准备开始？

前往下一页了解如何安装 TanStack Start 并创建您的第一个应用！
