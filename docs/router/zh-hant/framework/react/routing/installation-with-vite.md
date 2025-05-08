---
source-updated-at: '2025-04-13T21:10:42.000Z'
translation-updated-at: '2025-05-08T20:16:59.208Z'
title: 使用 Vite 安裝
---

[//]: # 'BundlerConfiguration'

要搭配 **Vite** 使用檔案式路由 (file-based routing)，您需要安裝 `@tanstack/router-plugin` 套件。

```sh
npm install -D @tanstack/router-plugin
```

安裝完成後，您需要將外掛 (plugin) 加入 Vite 設定檔中。

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    // 請確保 '@tanstack/router-plugin' 在 '@vitejs/plugin-react' 之前傳入
    TanStackRouterVite({ target: 'react', autoCodeSplitting: true }),
    react(),
    // ...
  ],
})
```

或者，您可以複製我們的 [Vite 快速入門範例](https://github.com/TanStack/router/tree/main/examples/react/quickstart-file-based) 直接開始使用。

> [!WARNING]
> 如果您正在使用舊版的 `@tanstack/router-vite-plugin` 套件，仍可繼續使用，因為它會被別名 (alias) 指向 `@tanstack/router-plugin/vite` 套件。但我們建議直接使用 `@tanstack/router-plugin` 套件。

現在您已將外掛加入 Vite 設定中，可以開始使用 TanStack Router 的檔案式路由功能了。

[//]: # 'BundlerConfiguration'

## 忽略自動產生的路由樹檔案

如果您的專案配置了程式碼檢查工具 (linter) 或格式化工具 (formatter)，您可能會想忽略自動產生的路由樹檔案。此檔案由 TanStack Router 管理，因此不應被您的檢查工具或格式化工具修改。

以下是一些協助您忽略路由樹檔案的資源：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，在重新命名路由後，可能會遇到路由樹檔案意外開啟（並顯示錯誤）的情況。

您可以透過 VSCode 設定將該檔案標記為唯讀來防止此情況。我們建議同時透過以下設定將其從搜尋結果和檔案監控中排除：

```json
{
  "files.readonlyInclude": {
    "**/routeTree.gen.ts": true
  },
  "files.watcherExclude": {
    "**/routeTree.gen.ts": true
  },
  "search.exclude": {
    "**/routeTree.gen.ts": true
  }
}
```

您可以將這些設定套用至使用者層級，或僅針對單一工作區 (workspace) 使用，方法是在專案根目錄建立 `.vscode/settings.json` 檔案。

## 設定

當搭配 Vite 使用 TanStack Router 外掛進行檔案式路由時，預設提供了一些適合多數專案的合理設定：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果這些預設值符合您的專案需求，您完全不需要進行任何設定！但如果您需要自訂配置，可以透過編輯傳入 `TanStackRouterVite` 函式的設定物件來實現。

您可以在 [檔案式路由 API 參考文件](../../../api/file-based-routing.md) 中找到所有可用的設定選項。
