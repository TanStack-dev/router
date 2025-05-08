---
source-updated-at: '2025-03-27T00:19:57.000Z'
translation-updated-at: '2025-05-08T20:25:58.397Z'
id: hosting
title: 託管服務 (Hosting)
---

託管 (Hosting) 是將您的應用程式部署到網際網路上，讓使用者能夠存取的重要流程。這是任何網頁開發專案中關鍵的一環，確保您的應用程式能夠被全球使用者使用。TanStack Start 建構於 [Nitro](https://nitro.unjs.io/) 之上，這是一個強大的伺服器工具組，可將網頁應用程式部署到任何地方。Nitro 讓 TanStack Start 能夠在任何託管服務提供商上提供統一的 API，用於伺服器渲染 (SSR)、串流 (streaming) 和 hydration。

## 我該選擇什麼？

TanStack Start **設計成可與任何託管服務提供商相容**，因此如果您已有偏好的託管服務商，您可以使用 TanStack Start 提供的全端 (full-stack) API 將應用程式部署到該平台。

然而，由於託管是影響應用程式效能、可靠性和擴展性最關鍵的因素之一，我們強烈推薦使用我們的官方託管合作夥伴 [Netlify](https://www.netlify.com?utm_source=tanstack)。

## 什麼是 Netlify？

<a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
  </picture>
</a>

Netlify 是一個領先的託管平台，提供快速、安全且可靠的環境來部署您的網頁應用程式。透過 Netlify，您只需點擊幾下即可部署 TanStack Start 應用程式，並享受全球邊緣網路 (edge network)、自動擴展 (automatic scaling) 以及與 GitHub 和 GitLab 無縫整合等功能。Netlify 旨在讓您的開發流程從本地開發到生產部署都無比順暢。

- 了解更多關於 Netlify 的資訊，請造訪 [Netlify 官方網站](https://www.netlify.com?utm_source=tanstack)
- 註冊帳號，請前往 [Netlify 儀表板](https://www.netlify.com/signup?utm_source=tanstack)

## 部署

> [!WARNING]
> 本頁面仍在建置中。我們將持續更新此頁面，提供部署到不同託管服務商的指南！

當部署 TanStack Start 應用程式時，`app.config.ts` 檔案中的 `server.preset` 值決定了部署目標。部署目標可設定為以下其中一個值：

- [`netlify`](#netlify): 部署到 Netlify
- [`vercel`](#vercel): 部署到 Vercel
- [`cloudflare-pages`](#cloudflare-pages): 部署到 Cloudflare Pages
- [`node-server`](#nodejs): 部署到 Node.js 伺服器
- [`bun`](#bun): 部署到 Bun 伺服器
- ... 更多選項即將推出！

選擇部署目標後，您可以按照以下部署指南將 TanStack Start 應用程式部署到您選擇的託管服務商。

### Netlify

在 `app.config.ts` 檔案中將 `server.preset` 值設為 `netlify`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'netlify',
  },
})
```

或者您可以在建置應用程式時使用 `--preset` 標記指定部署目標：

```sh
npm run build --preset netlify
```

使用 Netlify 的一鍵部署流程來部署您的應用程式，即可完成！

### Vercel

將 TanStack Start 應用程式部署到 Vercel 非常簡單。只需在 `app.config.ts` 檔案中將 `server.preset` 值設為 `vercel`，即可準備部署到 Vercel。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'vercel',
  },
})
```

或者您可以在建置應用程式時使用 `--preset` 標記指定部署目標：

```sh
npm run build --preset vercel
```

使用 Vercel 的一鍵部署流程來部署您的應用程式，即可完成！

### Cloudflare Pages

部署到 Cloudflare Pages 時，您需要完成幾個額外步驟才能讓使用者開始使用您的應用程式。

1. 安裝

首先您需要安裝 `unenv`：

```sh
npm install unenv
```

2. 更新 `app.config.ts`

在 `app.config.ts` 檔案中將 `server.preset` 值設為 `cloudflare-pages`，並將 `server.unenv` 值設為 `unenv` 中的 `cloudflare`。

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

3. 新增 `wrangler.toml` 設定檔

```toml
# wrangler.toml
name = "your-cloudflare-project-name"
pages_build_output_dir = "./dist"
compatibility_flags = ["nodejs_compat"]
compatibility_date = "2024-11-13"
```

使用 Cloudflare Pages 的一鍵部署流程來部署您的應用程式，即可完成！

### Node.js

在 `app.config.ts` 檔案中將 `server.preset` 值設為 `node-server`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'node-server',
  },
})

// 或者您可以在建置應用程式時使用 --preset 標記指定部署目標：
// npm run build --preset node-server
```

接著執行以下指令來建置並啟動您的應用程式：

```sh
npm run build
```

現在您可以將應用程式部署到 Node.js 伺服器。執行以下指令來啟動應用程式：

```sh
node .output/server/index.mjs
```

### Bun

請確認您的 `package.json` 檔案中有 `solid-js`。如果沒有，請執行以下指令來升級套件：

```sh
npm install solid-js
```

在 `app.config.ts` 檔案中將 `server.preset` 值設為 `bun`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/solid-start/config'

export default defineConfig({
  server: {
    preset: 'bun',
  },
})

// 或者您可以在建置應用程式時使用 --preset 標記指定部署目標：
// npm run build --preset bun
```

接著執行以下指令來建置並啟動您的應用程式：

```sh
bun run build
```

現在您可以將應用程式部署到 Bun 伺服器。執行以下指令來啟動應用程式：

```sh
bun run .output/server/index.mjs
```
