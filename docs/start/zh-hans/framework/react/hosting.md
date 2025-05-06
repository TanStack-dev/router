---
source-updated-at: '2025-05-01T02:26:59.000Z'
translation-updated-at: '2025-05-06T22:19:28.728Z'
id: hosting
title: 托管部署
---

托管 (Hosting) 是将您的应用程序部署到互联网上供用户访问的过程。这是任何 Web 开发项目的关键环节，确保您的应用能够面向全球可用。TanStack Start 基于 [Nitro](https://nitro.unjs.io/) 构建，这是一个强大的服务器工具包，可在任何地方部署 Web 应用。Nitro 使 TanStack Start 能够为任何托管服务商提供统一的 服务端渲染 (SSR)、流式传输 (streaming) 和注水 (hydration) API。

## 应该选择什么？

TanStack Start **设计为兼容任何托管服务商**，如果您已有心仪的托管平台，可以直接使用 TanStack Start 提供的全栈 API 进行部署。

但由于托管服务直接影响应用的性能、可靠性和扩展性，我们强烈推荐使用官方托管合作伙伴 [Netlify](https://www.netlify.com?utm_source=tanstack)。

## 什么是 Netlify？

<a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
  </picture>
</a>

Netlify 是领先的托管平台，为 Web 应用部署提供快速、安全且可靠的环境。通过 Netlify，您只需点击几下即可部署 TanStack Start 应用，并享受全球边缘网络、自动扩展以及与 GitHub/GitLab 无缝集成等特性。Netlify 旨在让您的开发流程从本地调试到生产部署都无比顺畅。

- 了解更多：[Netlify 官网](https://www.netlify.com?utm_source=tanstack)
- 立即注册：[Netlify 控制台](https://www.netlify.com/signup?utm_source=tanstack)

## 部署指南

> [!WARNING]
> 本页面仍在完善中。我们将持续更新不同托管服务商的部署指南！

部署 TanStack Start 应用时，`app.config.ts` 文件中的 `server.preset` 值决定了部署目标。可选的部署目标包括：

- [`netlify`](#netlify): 部署到 Netlify
- [`vercel`](#vercel): 部署到 Vercel
- [`cloudflare-pages`](#cloudflare-pages): 部署到 Cloudflare Pages
- [`node-server`](#nodejs): 部署到 Node.js 服务器
- [`bun`](#bun): 部署到 Bun 服务器
- ... 更多选项即将推出！

选定部署目标后，您可参照以下指南将应用部署到对应平台。

### Netlify

在 `app.config.ts` 中设置 `server.preset` 为 `netlify`：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'netlify',
  },
})
```

或使用 `build` 命令时通过 `--preset` 参数指定：

```sh
npm run build --preset netlify
```

通过 Netlify 的一键部署流程完成发布即可。

### Vercel

在 `app.config.ts` 中设置 `server.preset` 为 `vercel`：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'vercel',
  },
})
```

或使用构建命令时指定：

```sh
npm run build --preset vercel
```

通过 Vercel 的一键部署流程完成发布。

### Cloudflare Pages

部署到 Cloudflare Pages 需要额外配置：

1. 安装依赖

首先安装 `unenv`：

```sh
npm install unenv
```

2. 更新配置文件

在 `app.config.ts` 中设置参数：

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

最后通过 Cloudflare Pages 的一键部署完成发布。

### Node.js

在 `app.config.ts` 中设置 `server.preset` 为 `node-server`：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'node-server',
  },
})

// 或通过构建命令指定：
// npm run build --preset node-server
```

构建并启动应用：

```sh
npm run build
node .output/server/index.mjs
```

### Bun

> [!IMPORTANT]
> 当前 Bun 部署指南仅支持 React 19。若使用 React 18 请参照 [Node.js](#nodejs) 指南。

确保 `package.json` 中 React 相关依赖版本 ≥ 19.0.0，否则执行：

```sh
npm install react@rc react-dom@rc
```

在 `app.config.ts` 中设置 `server.preset` 为 `bun`：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'bun',
  },
})

// 或通过构建命令指定：
// npm run build --preset bun
```

构建并启动应用：

```sh
bun run build
bun run .output/server/index.mjs
```
