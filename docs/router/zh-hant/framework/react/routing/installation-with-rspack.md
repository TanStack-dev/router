---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-05-08T20:16:54.075Z'
title: 使用 Rspack/Rsbuild 安裝
---

[//]: # 'BundlerConfiguration'

要在 **Rspack** 或 **Rsbuild** 中使用基於檔案的路由 (file-based routing)，您需要安裝 `@tanstack/router-plugin` 套件。

```sh
npm install -D @tanstack/router-plugin
```

安裝完成後，您需要將插件加入設定檔中。

```tsx
// rsbuild.config.ts
import { defineConfig } from '@rsbuild/core'
import { pluginReact } from '@rsbuild/plugin-react'
import { TanStackRouterRspack } from '@tanstack/router-plugin/rspack'

export default defineConfig({
  plugins: [pluginReact()],
  tools: {
    rspack: {
      plugins: [
        TanStackRouterRspack({ target: 'react', autoCodeSplitting: true }),
      ],
    },
  },
})
```

或者，您可以複製我們的 [Rspack/Rsbuild 快速入門範例](https://github.com/TanStack/router/tree/main/examples/react/quickstart-rspack-file-based) 直接開始使用。

現在您已將插件加入 Rspack/Rsbuild 設定中，可以開始使用 TanStack Router 的基於檔案路由功能了。

[//]: # 'BundlerConfiguration'

## 忽略自動生成的路由樹檔案

如果您的專案配置了程式碼檢查工具 (linter) 或格式化工具 (formatter)，您可能會想忽略自動生成的路由樹檔案。這個檔案由 TanStack Router 管理，因此不應被您的檢查工具或格式化工具修改。

以下是一些幫助您忽略路由樹檔案的資源：

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

您可以將這些設定套用於使用者層級，或僅針對單一工作區使用，方法是在專案根目錄建立 `.vscode/settings.json` 檔案。

## 設定選項

當在 Rspack（或 Rsbuild）中使用 TanStack Router 插件進行基於檔案的路由時，它提供了一些適合大多數專案的預設值：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果這些預設值符合您的專案需求，您完全不需要進行任何設定！但如果您需要自訂設定，可以透過編輯傳入 `TanStackRouterVite` 函式的設定物件來實現。

您可以在 [基於檔案路由的 API 參考文件](../../../api/file-based-routing.md) 中找到所有可用的設定選項。
