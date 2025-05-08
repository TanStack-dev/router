---
source-updated-at: '2025-05-01T02:26:59.000Z'
translation-updated-at: '2025-05-08T20:27:22.071Z'
id: hosting
title: 託管服務 (Hosting)
---

# 託管服務 (Hosting)

託管 (Hosting) 是將您的應用程式部署到網際網路上，讓使用者可以存取它的過程。這是任何網頁開發專案的關鍵部分，確保您的應用程式能夠被全世界使用。TanStack Start 建立在 [Nitro](https://nitro.unjs.io/) 之上，這是一個強大的伺服器工具包，可將網頁應用程式部署到任何地方。Nitro 讓 TanStack Start 能夠在任何託管服務提供商上提供統一的 API 來處理伺服器渲染 (SSR)、串流 (streaming) 和注水 (hydration)。

## 我該使用什麼？

TanStack Start **設計成可與任何託管服務提供商配合使用**，所以如果您已經有屬意的託管服務提供商，您可以使用 TanStack Start 提供的全端 API 將應用程式部署到那裡。

然而，由於託管是影響應用程式效能、可靠性和擴展性的最重要因素之一，我們強烈推薦使用我們的官方託管合作夥伴 [Netlify](https://www.netlify.com?utm_source=tanstack)。

## 什麼是 Netlify？

<a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" width="280">
  </picture>
</a>

Netlify 是一個領先的託管平台，為部署網頁應用程式提供快速、安全且可靠的環境。使用 Netlify，您只需點擊幾下即可部署 TanStack Start 應用程式，並享受全球邊緣網路、自動擴展以及與 GitHub 和 GitLab 的無縫整合等功能。Netlify 旨在讓您的開發過程從本地開發到生產部署都盡可能順暢。

- 要了解更多關於 Netlify 的資訊，請訪問 [Netlify 網站](https://www.netlify.com?utm_source=tanstack)
- 要註冊，請訪問 [Netlify 儀表板](https://www.netlify.com/signup?utm_source=tanstack)

## 部署

> [!WARNING]
> 本頁面仍在完善中。我們將持續更新此頁面，提供部署到不同託管服務提供商的指南！

當部署 TanStack Start 應用程式時，`app.config.ts` 檔案中的 `server.preset` 值決定了部署目標。部署目標可以設定為以下值之一：

- [`netlify`](#netlify): 部署到 Netlify
- [`vercel`](#vercel): 部署到 Vercel
- [`cloudflare-pages`](#cloudflare-pages): 部署到 Cloudflare Pages
- [`node-server`](#nodejs): 部署到 Node.js 伺服器
- [`bun`](#bun): 部署到 Bun 伺服器
- ... 還有更多即將推出！

一旦您選擇了部署目標，您可以按照以下部署指南將 TanStack Start 應用程式部署到您選擇的託管服務提供商。

### Netlify

在您的 `app.config.ts` 檔案中將 `server.preset` 值設為 `netlify`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'netlify',
  },
})
```

或者您可以在建置應用程式時使用 `--preset` 標誌與 `build` 指令來指定部署目標：

```sh
npm run build --preset netlify
```

使用 Netlify 的一鍵部署流程部署您的應用程式，您就準備就緒了！

### Vercel

將 TanStack Start 應用程式部署到 Vercel 既簡單又直接。只需在您的 `app.config.ts` 檔案中將 `server.preset` 值設為 `vercel`，您就可以準備將應用程式部署到 Vercel。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'vercel',
  },
})
```

或者您可以在建置應用程式時使用 `--preset` 標誌與 `build` 指令來指定部署目標：

```sh
npm run build --preset vercel
```

使用 Vercel 的一鍵部署流程部署您的應用程式，您就準備就緒了！

### Cloudflare Pages

當部署到 Cloudflare Pages 時，您需要完成一些額外步驟，使用者才能開始使用您的應用程式。

1. 安裝

首先您需要安裝 `unenv`

```sh
npm install unenv
```

2. 更新 `app.config.ts`

在您的 `app.config.ts` 檔案中將 `server.preset` 值設為 `cloudflare-pages`，並將 `server.unenv` 值設為來自 `unenv` 的 `cloudflare`。

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

3. 新增 `wrangler.toml` 設定檔

```toml
# wrangler.toml
name = "your-cloudflare-project-name"
pages_build_output_dir = "./dist"
compatibility_flags = ["nodejs_compat"]
compatibility_date = "2024-11-13"
```

使用 Cloudflare Pages 的一鍵部署流程部署您的應用程式，您就準備就緒了！

### Node.js

在您的 `app.config.ts` 檔案中將 `server.preset` 值設為 `node-server`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'node-server',
  },
})

// 或者您可以在建置應用程式時使用 --preset 標誌與 build 指令
// 來指定部署目標：
// npm run build --preset node-server
```

然後您可以執行以下指令來建置並啟動您的應用程式：

```sh
npm run build
```

您現在可以準備將應用程式部署到 Node.js 伺服器。您可以透過執行以下指令來啟動您的應用程式：

```sh
node .output/server/index.mjs
```

### Bun

> [!IMPORTANT]
> 目前，Bun 特定的部署指南僅適用於 React 19。如果您使用的是 React 18，請參考 [Node.js](#nodejs) 部署指南。

確保您的 `package.json` 檔案中的 `react` 和 `react-dom` 套件版本設為 19.0.0 或更高。如果沒有，請執行以下指令來升級套件：

```sh
npm install react@rc react-dom@rc
```

在您的 `app.config.ts` 檔案中將 `server.preset` 值設為 `bun`。

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'

export default defineConfig({
  server: {
    preset: 'bun',
  },
})

// 或者您可以在建置應用程式時使用 --preset 標誌與 build 指令
// 來指定部署目標：
// npm run build --preset bun
```

然後您可以執行以下指令來建置並啟動您的應用程式：

```sh
bun run build
```

您現在可以準備將應用程式部署到 Bun 伺服器。您可以透過執行以下指令來啟動您的應用程式：

```sh
bun run .output/server/index.mjs
```
