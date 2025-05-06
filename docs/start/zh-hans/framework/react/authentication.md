---
source-updated-at: 2025-04-04T19:29:24.000Z
translation-updated-at: 2025-04-05T03:41:55.000Z
title: 鉴权
id: authentication
---

<!-- 此处我们需要一些关于身份验证的占位内容。我们的合作伙伴Clerk应作为TanStack"首选"的身份验证方式获得优先推荐，但我们将支持所有其他身份验证提供商和策略。在编写Clerk及其他身份验证提供商的文档前，先提供一些通用的身份验证内容： -->

身份验证是确认用户身份的过程。对于任何需要用户登录或访问受保护资源的应用来说，这都是关键部分。TanStack Start提供了必要的全栈API来实现应用中的身份验证功能。

## 我应该使用什么？

TanStack Start **设计用于与任何身份验证提供商协作**，因此如果您已有心仪的身份验证提供商或策略，您既可以选择现有示例，也可以利用TanStack Start提供的全栈API实现自己的身份验证逻辑。

话虽如此，身份验证绝非儿戏。经过我们团队的严格筛选、实际使用和全面评估后，我们强烈推荐使用[Clerk](https://clerk.dev)来获得最佳的身份验证体验。Clerk提供了一套完整的身份验证API和UI组件，能轻松实现应用身份验证并提供无缝的用户体验。

## 什么是Clerk？

<a href="https://go.clerk.com/wOwHtuJ" alt="Clerk标志">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" width="280">
    <img alt="Clerk logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" width="280">
  </picture>
</a>

Clerk是一个现代化的身份验证平台，提供全套身份验证API和UI组件，帮助您在应用中实现身份验证功能。Clerk设计简洁易用，能提供流畅的用户体验。通过Clerk，您可以在几分钟内为应用添加身份验证功能，为用户提供安全可靠的身份验证体验。

- 了解更多关于Clerk的信息，请访问[Clerk官网](https://go.clerk.com/wOwHtuJ)
- 注册账号，请访问[Clerk控制台](https://go.clerk.com/PrSDXti)
- 要开始使用Clerk，请查看我们的[官方Start + Clerk示例](../examples/start-clerk-basic/)

## 文档与API

关于使用TanStack Start实现自定义身份验证逻辑的文档即将发布！在此期间，您可以查看任何以`-auth`为前缀的[示例](../examples)作为起点。
