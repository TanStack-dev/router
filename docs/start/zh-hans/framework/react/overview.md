---
source-updated-at: 2025-02-27T22:43:21.000Z
translation-updated-at: 2025-04-05T03:40:13.000Z
title: 概述
id: overview
---

TanStack Start 是一个由 TanStack Router 驱动的全栈 React 框架。它通过 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 等工具提供完整的文档 SSR（服务器端渲染）、流式传输、服务器函数、打包等功能，并支持部署到您喜欢的托管平台！

## Router 还是 Start？

TanStack Router 是一个强大、类型安全且功能齐全的 React 应用路由系统，专为轻松应对最复杂的全栈路由需求而设计。TanStack Start 在 Router 的类型系统基础上构建，提供类型安全的全栈 API，助您高效开发。

TanStack Router 提供以下功能：

- 100% 类型推断的 TypeScript 支持
- 类型安全的导航
- 嵌套路由和无路径布局路由
- 内置路由加载器及 SWR 缓存
- 专为客户端数据缓存设计（兼容 TanStack Query、SWR 等）
- 自动路由预加载
- 异步路由组件和错误边界
- 基于文件的路由生成
- 类型安全的 JSON 优先搜索参数状态管理 API
- 路径和搜索参数的模式验证
- 搜索参数导航 API
- 自定义搜索参数解析器/序列化器支持
- 搜索参数中间件
- 路由匹配/加载中间件

TanStack Start 提供以下功能：

- 完整的文档 SSR
- 流式传输
- 服务器函数 / RPC
- 打包
- 部署
- 全栈类型安全

**总结：TanStack Router 适用于客户端路由，而 TanStack Start 适用于全栈路由。**

## 工作原理

TanStack Start 使用 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 打包并部署您的应用。实际上，这些工具同样驱动着 Solid Start！借助这些工具，我们可以实现以下新功能：

- 为 SSR、流式传输和水合提供统一的 API
- 从客户端代码中提取仅限服务器的代码（如服务器函数）
- 为应用打包以部署到任何托管平台

## 适用场景

TanStack Start 非常适合需要构建全栈 React 应用并满足以下需求的开发者：

- 完整的文档 SSR 和水合
- 流式传输
- 服务器函数 / RPC
- 全栈类型安全
- 强大的路由功能
- 丰富的客户端交互性

## 不适用场景

TanStack Start 可能不适合以下情况：

- 您的网站完全静态
- 您的目标是零 JS 或极少客户端交互的服务器渲染网站
- 您需要以 React Server Component 为核心的框架（我们即将推出独特的 RSC 支持！）

## 资金支持

TanStack 与合作伙伴紧密协作，提供最佳的开发体验，同时确保解决方案普适且经过行业专家验证。每位合作伙伴在 TanStack 生态中扮演独特角色：

- **Netlify**  
  <a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" style="height: 90px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">  
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">  
  </picture>  
  </a>  
  领先的 Web 应用托管平台，提供快速、安全、可靠的部署环境。我们与 Netlify 深度合作，确保 TanStack Start 应用不仅能无缝部署到其平台，还能实现性能、安全性和可靠性的最佳实践，无论最终部署到哪里。

- **Clerk**  
  <a href="https://go.clerk.com/wOwHtuJ" alt="Clerk Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" style="height: 40px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">  
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">  
  </picture>  
  </a>  
  为现代 Web 应用（包括 TanStack Start 应用）提供最佳的认证体验。Clerk 为 TanStack Start 用户提供一流的身份验证集成方案，并与 TanStack 团队紧密合作，确保 API 符合最新的身份验证最佳实践。

- **Convex**  
  <a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" style="height: 40px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">  
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">  
  </picture>  
  </a>  
  与 TanStack Start 无缝集成的无服务器数据库平台。Convex 简化了应用数据管理流程，提供实时、可扩展且支持事务的数据后端，完美适配 TanStack Start 应用。Convex 还与 TanStack 团队密切协作，确保数据库相关 API 符合最新最佳实践。

- **Sentry**  
  <a href="https://sentry.io?utm_source=tanstack" alt='Sentry Logo'>  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-dark.svg" style="height: 60px;">  
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">  
  </picture>  
  </a>  
  功能强大的可观测性平台，与 TanStack Start 无缝集成。Sentry 帮助开发者实时监控并修复崩溃问题，提供应用性能分析和错误追踪洞察。Sentry 与 TanStack 团队紧密合作，确保可观测性相关 API 符合最新最佳实践。

## 准备开始？

前往下一页，了解如何安装 TanStack Start 并创建您的第一个应用！
