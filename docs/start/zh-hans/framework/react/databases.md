---
source-updated-at: 2025-04-04T19:29:24.000Z
translation-updated-at: 2025-04-05T03:42:39.000Z
title: 数据库
id: databases
---

数据库是任何动态应用的核心，它为数据的存储、检索和管理提供了必要的基础设施。TanStack Start 让与各类数据库的集成变得简单，为应用数据层管理提供了灵活方案。

## 如何选择数据库？

TanStack Start **设计兼容所有数据库服务商**，若您已有偏好的数据库系统，可通过提供的全栈API将其与TanStack Start集成。无论您使用SQL、NoSQL还是其他类型数据库，TanStack Start都能满足需求。

## 集成数据库有多简单？

在TanStack Start中使用数据库，只需从服务端函数或路由调用数据库适配器/客户端/驱动/服务即可。以下是一个连接数据库并进行读写的抽象示例：

```tsx
import { createServerFn } from '@tanstack/react-start'

const db = createMyDatabaseClient()

export const getUser = createServerFn(async ({ ctx }) => {
  const user = await db.getUser(ctx.userId)
  return user
})

export const createUser = createServerFn(async ({ ctx, input }) => {
  const user = await db.createUser(input)
  return user
})
```

虽然示例经过简化，但它证明了只要能在服务端函数或路由中调用，TanStack Start可以与任何数据库服务商无缝协作。

## 推荐数据库服务商

虽然TanStack Start兼容所有数据库服务商，但我们强烈推荐考虑经过认证的合作伙伴[Neon](https://neon.tech?utm_source=tanstack)或[Convex](https://convex.dev?utm_source=tanstack)。它们均通过TanStack的质量、开放性和性能标准认证，是数据库需求的绝佳选择。

## Neon是什么？

<a href="https://neon.tech?utm_source=tanstack" alt="Neon Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" width="280">
    <img alt="Neon logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" width="280">
  </picture>
</a>

Neon是全托管的无服务器PostgreSQL服务，提供慷慨的免费额度。其存储与计算分离架构支持自动扩展、分支功能和无限存储。Neon既具备PostgreSQL的强大与可靠，又拥有现代云能力，是TanStack Start应用的理想选择。

Neon的突出特性包括：

- 自动扩展的无服务器PostgreSQL
- 支持开发测试的数据库分支
- 内置连接池
- 时间点恢复
- 网页版SQL编辑器
- 无限存储

- 了解更多请访问[Neon官网](https://neon.tech?utm_source=tanstack)
- 注册请前往[Neon控制台](https://console.neon.tech/sign_up?utm_source=tanstack)

## Convex是什么？

<a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" width="280">
    <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" width="280">
  </picture>
</a>

Convex是强大的无服务器数据库平台，能简化应用数据管理流程。使用Convex开发全栈应用时，无需手动管理数据库服务器或编写复杂查询。其实时、可扩展且支持事务的数据后端与TanStack Start无缝集成，是现代Web应用的优质选择。

Convex的声明式数据模型和自动冲突解决机制能确保应用始终保持一致性和响应速度，即使在高负载下也不例外。其设计以开发体验为核心，注重简洁与高效。

- 了解更多请访问[Convex官网](https://convex.dev?utm_source=tanstack)
- 注册请前往[Convex控制台](https://dashboard.convex.dev/signup?utm_source=tanstack)

## 文档与API

不同数据库与TanStack Start的集成文档即将发布！在此期间，请持续关注我们的示例和指南，了解如何在TanStack Start应用中充分发挥数据层潜力。
