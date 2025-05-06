---
source-updated-at: 2025-02-25T21:17:33.000Z
translation-updated-at: 2025-04-05T03:41:48.000Z
title: 托管
id: hosting
---

托管部署是将您的应用程序发布到互联网上，以便用户能够访问的过程。这是任何网页开发项目的关键环节，确保您的应用程序能够面向全球提供服务。TanStack Start 基于 [Nitro](https://nitro.unjs.io/) 构建，这是一个功能强大的服务器工具包，能够将网页应用程序部署到任何地方。Nitro 使 TanStack Start 能够为任何托管服务提供商提供 SSR、流式传输和 hydration 的统一 API。

## 我应该使用什么？

TanStack Start **设计为与任何托管服务提供商兼容**，因此如果您已有心仪的托管服务提供商，您可以使用 TanStack Start 提供的全栈 API 将应用程序部署到那里。

然而，由于托管是影响应用程序性能、可靠性和可扩展性的最关键因素之一，我们强烈推荐使用我们的官方托管合作伙伴 [Netlify](https://www.netlify.com?utm_source=tanstack)。

## 什么是 Netlify？

<a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
  </picture>
</a>

Netlify 是一个领先的托管平台，为您的网页应用程序提供快速、安全且可靠的部署环境。通过 Netlify，您只需点击几下即可部署您的 TanStack Start 应用程序，并享受全球边缘网络、自动扩展以及与 GitHub 和 GitLab 无缝集成等功能。Netlify 旨在让您的开发流程从本地开发到生产部署尽可能顺畅。

- 要了解更多关于 Netlify 的信息，请访问 [Netlify 官网](https://www.netlify.com?utm_source=tanstack)
- 要注册，请访问 [Netlify 控制台](https://www.netlify.com/signup?utm_source=tanstack)

## 部署

> [!WARNING]
> 本页面仍在完善中。我们将持续更新此页面，提供更多关于不同托管服务提供商的部署指南！

当部署 TanStack Start 应用程序时，`app.config.ts` 文件中的 `server.preset` 值决定了部署目标。部署目标可以设置为以下值之一：

- [`netlify`](#netlify): 部署到 Netlify
- [`vercel`](#vercel): 部署到 Vercel
- [`cloudflare-pages`](#cloudflare-pages): 部署到 Cloudflare Pages
- [`node-server`](#nodejs): 部署到 Node.js 服务器
- [`bun`](#bun): 部署到 Bun 服务器
- ... 更多即将推出！

一旦您选择了部署目标，您可以按照以下部署指南将您的 TanStack Start 应用程序部署到您选择的托管服务提供商。

### Netlify

在您的 `app.config.ts` 文件中将 `server.preset` 值设置为 `netlify`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'netlify',
  },
})
```

或者您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志来指定部署目标：

```sh
npm run build --preset netlify
```

使用 Netlify 的一键部署流程部署您的应用程序，即可完成！

### Vercel

将您的 TanStack Start 应用程序部署到 Vercel 非常简单直接。只需在您的 `app.config.ts` 文件中将 `server.preset` 值设置为 `vercel`，即可准备将应用程序部署到 Vercel。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'vercel',
  },
})
```

或者您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志来指定部署目标：

```sh
npm run build --preset vercel
```

使用 Vercel 的一键部署流程部署您的应用程序，即可完成！

### Cloudflare Pages

部署到 Cloudflare Pages 时，您需要完成一些额外的步骤，用户才能开始使用您的应用程序。

1. 安装

首先您需要安装 `unenv`

```sh
npm install unenv
```

2. 更新 `app.config.ts`

在您的 `app.config.ts` 文件中将 `server.preset` 值设置为 `cloudflare-pages`，并将 `server.unenv` 值设置为 `unenv` 中的 `cloudflare`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'
import { cloudflare } from 'unenv'

export default defineConfig({
  server: {
    preset: 'cloudflare-pages',
    unenv: cloudflare,
  },
})
```

3. 添加 `wrangler.toml` 配置文件

```toml
# wrangler.toml
name = "your-cloudflare-project-name"
pages_build_output_dir = "./dist"
compatibility_flags = ["nodejs_compat"]
compatibility_date = "2024-11-13"
```

使用 Cloudflare Pages 的一键部署流程部署您的应用程序，即可完成！

### Node.js

在您的 `app.config.ts` 文件中将 `server.preset` 值设置为 `node-server`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'node-server',
  },
})

// 或者您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志
// 来指定部署目标：
// npm run build --preset node-server
```

然后您可以运行以下命令来构建并启动您的应用程序：

```sh
npm run build
```

现在您可以将应用程序部署到 Node.js 服务器。通过运行以下命令启动您的应用程序：

```sh
node .output/server/index.mjs
```

### Bun

> [!IMPORTANT]
> 目前，Bun 特定的部署指南仅适用于 React 19。如果您使用的是 React 18，请参考 [Node.js](#nodejs) 部署指南。

确保您的 `package.json` 文件中的 `react` 和 `react-dom` 包版本设置为 19.0.0 或更高。如果没有，请运行以下命令升级这些包：

```sh
npm install react@rc react-dom@rc
```

在您的 `app.config.ts` 文件中将 `server.preset` 值设置为 `bun`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'bun',
  },
})

// 或者您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志
// 来指定部署目标：
// npm run build --preset bun
```

然后您可以运行以下命令来构建并启动您的应用程序：

```sh
bun run build
```

现在您可以将应用程序部署到 Bun 服务器。通过运行以下命令启动您的应用程序：

```sh
bun run .output/server/index.mjs
```
