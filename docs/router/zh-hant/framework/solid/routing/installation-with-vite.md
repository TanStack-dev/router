---
source-updated-at: '2025-02-27T01:26:11.000Z'
translation-updated-at: '2025-05-08T20:15:32.378Z'
title: 使用 Vite 安裝
---

# 使用 Vite 安裝

要搭配 **Vite** 使用基於檔案的路由 (file-based routing)，您需要安裝 `@tanstack/router-plugin` 套件。

```sh
npm install -D @tanstack/router-plugin
```

安裝完成後，您需要將插件加入 Vite 配置中。

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import solid from 'vite-plugin-solid'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    TanStackRouterVite({ target: 'solid', autoCodeSplitting: true }),
    solid(),
    // ...
  ],
})
```

如果您使用 TypeScript，還需要在 `tsconfig.json` 中加入以下選項：

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "solid-js"
  }
}
```

或者，您可以克隆我們的 [快速開始 Vite 範例](https://github.com/TanStack/router/tree/main/examples/solid/quickstart-file-based) 直接開始使用。

現在您已將插件加入 Vite 配置中，可以開始使用 TanStack Router 的基於檔案路由功能了。

## 忽略生成的路由樹檔案

如果您的專案配置了 linter 或程式碼格式化工具 (formatter)，您可能會想忽略生成的路由樹檔案。這個檔案由 TanStack Router 管理，因此不應被您的 linter 或 formatter 修改。

以下是一些幫助您忽略生成路由樹檔案的資源：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，在重新命名路由後，可能會遇到路由樹檔案意外開啟（並顯示錯誤）的情況。

您可以透過 VSCode 設定將該檔案標記為唯讀來防止這種情況。我們建議同時透過以下設定將其從搜尋結果和檔案監視器中排除：

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

您可以將這些設定應用於使用者層級，或僅針對單一工作區，方法是在專案根目錄建立 `.vscode/settings.json` 檔案。

## 配置

當搭配 Vite 使用 TanStack Router 插件進行基於檔案的路由時，它提供了一些適合大多數專案的預設配置：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果這些預設值適合您的專案，您完全不需要進行任何配置！但如果您需要自訂配置，可以透過編輯傳入 `TanStackRouterVite` 函式的配置物件來實現。

您可以在 [基於檔案路由的 API 參考文件](../../../api/file-based-routing.md) 中找到所有可用的配置選項。
