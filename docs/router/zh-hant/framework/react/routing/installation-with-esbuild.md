---
source-updated-at: '2025-02-25T23:33:22.000Z'
translation-updated-at: '2025-05-08T20:16:52.093Z'
title: 使用 Esbuild 安裝
---

[//]: # 'BundlerConfiguration'

要使用 **Esbuild** 進行基於檔案的路由設定 (file-based routing)，您需要安裝 `@tanstack/router-plugin` 套件。

```sh
npm install -D @tanstack/router-plugin
```

安裝完成後，您需要將插件加入設定檔中。

```tsx
// esbuild.config.js
import { TanStackRouterEsbuild } from '@tanstack/router-plugin/esbuild'

export default {
  // ...
  plugins: [
    TanStackRouterEsbuild({ target: 'react', autoCodeSplitting: true }),
  ],
}
```

或者，您可以克隆我們的 [快速開始 Esbuild 範例](https://github.com/TanStack/router/tree/main/examples/react/quickstart-esbuild-file-based) 直接開始使用。

現在您已將插件加入 Esbuild 設定中，可以開始使用 TanStack Router 的基於檔案路由功能了。

[//]: # 'BundlerConfiguration'

## 忽略自動產生的路由樹檔案

如果您的專案配置了程式碼檢查工具 (linter) 或格式化工具，您可能需要忽略自動產生的路由樹檔案。這個檔案由 TanStack Router 管理，因此不應被您的檢查工具或格式化工具修改。

以下是一些幫助您忽略路由樹檔案的資源：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，在重新命名路由後可能會意外開啟路由樹檔案（並顯示錯誤）。

您可以透過 VSCode 設定將該檔案標記為唯讀來防止這種情況。我們建議同時透過以下設定將其排除在搜尋結果和檔案監控之外：

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

您可以在使用者層級或單一工作區中使用這些設定，只需在專案根目錄建立 `.vscode/settings.json` 檔案即可。

## 配置設定

當使用 TanStack Router 插件搭配 Esbuild 進行基於檔案的路由設定時，預設提供了一些適合多數專案的合理設定：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果這些預設值適用於您的專案，您完全不需要進行任何配置！但如果您需要自定義配置，可以透過編輯傳入 `TanStackRouterEsbuild` 函式的配置物件來實現。

您可以在 [基於檔案路由 API 參考文件](../../../api/file-based-routing.md) 中找到所有可用的配置選項。
