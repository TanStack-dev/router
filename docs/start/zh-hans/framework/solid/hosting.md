---
source-updated-at: 2025-03-27T00:19:57.000Z
translation-updated-at: 2025-04-05T03:43:37.000Z
title: 托管
id: hosting
---

托管部署是将您的应用程序发布到互联网上，以便用户可以访问的过程。这是任何网页开发项目的关键环节，确保您的应用程序能够面向全球用户。TanStack Start 基于 [Nitro](https://nitro.unjs.io/) 构建，这是一个强大的服务器工具包，可将网页应用程序部署到任何地方。Nitro 使 TanStack Start 能够在任何托管提供商上为 SSR（服务器端渲染）、流式传输和水合作用提供统一的 API。

## 我应该使用什么？

TanStack Start **设计为与任何托管提供商兼容**，因此如果您已有心仪的托管提供商，可以使用 TanStack Start 提供的全栈 API 将应用程序部署到那里。

然而，由于托管是影响应用程序性能、可靠性和可扩展性的最关键因素之一，我们强烈推荐使用我们的官方托管合作伙伴 [Netlify](https://www.netlify.com?utm_source=tanstack)。

## 什么是 Netlify？

<a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
  </picture>
</a>

Netlify 是一个领先的托管平台，为部署网页应用程序提供快速、安全且可靠的环境。通过 Netlify，您只需点击几下即可部署 TanStack Start 应用程序，并享受全球边缘网络、自动扩展以及与 GitHub 和 GitLab 无缝集成等功能。Netlify 旨在让您的开发流程从本地开发到生产部署尽可能顺畅。

- 要了解更多关于 Netlify 的信息，请访问 [Netlify 官网](https://www.netlify.com?utm_source=tanstack)
- 要注册，请访问 [Netlify 控制台](https://www.netlify.com/signup?utm_source=tanstack)

## 部署

> [!WARNING]
> 本页面仍在完善中。我们将持续更新此页面，提供针对不同托管提供商的部署指南！

当部署 TanStack Start 应用程序时，`app.config.ts` 文件中的 `server.preset` 值决定了部署目标。部署目标可以设置为以下值之一：

- [`netlify`](#netlify): 部署到 Netlify
- [`vercel`](#vercel): 部署到 Vercel
- [`cloudflare-pages`](#cloudflare-pages): 部署到 Cloudflare Pages
- [`node-server`](#nodejs): 部署到 Node.js 服务器
- [`bun`](#bun): 部署到 Bun 服务器
- ... 更多即将推出！

选定部署目标后，您可以按照以下部署指南将 TanStack Start 应用程序部署到您选择的托管提供商。

### Netlify

在 `app.config.ts` 文件中将 `server.preset` 值设置为 `netlify`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'netlify',
  },
})
```

或者，您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志来指定部署目标：

```sh
npm run build --preset netlify
```

通过 Netlify 的一键部署流程部署您的应用程序，即可完成！

### Vercel

将 TanStack Start 应用程序部署到 Vercel 非常简单直接。只需在 `app.config.ts` 文件中将 `server.preset` 值设置为 `vercel`，即可准备将应用程序部署到 Vercel。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'vercel',
  },
})
```

或者，您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志来指定部署目标：

```sh
npm run build --preset vercel
```

通过 Vercel 的一键部署流程部署您的应用程序，即可完成！

### Cloudflare Pages

部署到 Cloudflare Pages 时，您需要完成几个额外步骤，才能让用户开始使用您的应用程序。

1. 安装

首先需要安装 `unenv`

```sh
npm install unenv
```

2. 更新 `app.config.ts`

在 `app.config.ts` 文件中将 `server.preset` 值设置为 `cloudflare-pages`，并将 `server.unenv` 值设置为 `unenv` 中的 `cloudflare`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'
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

通过 Cloudflare Pages 的一键部署流程部署您的应用程序，即可完成！

### Node.js

在 `app.config.ts` 文件中将 `server.preset` 值设置为 `node-server`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'node-server',
  },
})

// 或者，您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志来指定部署目标：
// npm run build --preset node-server
```

然后运行以下命令构建并启动您的应用程序：

```sh
npm run build
```

现在，您可以将应用程序部署到 Node.js 服务器。通过运行以下命令启动应用程序：

```sh
node .output/server/index.mjs
```

### Bun

确保您的 `package.json` 文件中包含 `solid-js`。如果没有，请运行以下命令升级包：

```sh
npm install solid-js
```

在 `app.config.ts` 文件中将 `server.preset` 值设置为 `bun`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'bun',
  },
})

// 或者，您可以在构建应用程序时使用 `build` 命令的 `--preset` 标志来指定部署目标：
// npm run build --preset bun
```

然后运行以下命令构建并启动您的应用程序：

```sh
bun run build
```

现在，您可以将应用程序部署到 Bun 服务器。通过运行以下命令启动应用程序：

```sh
bun run .output/server/index.mjs
```
