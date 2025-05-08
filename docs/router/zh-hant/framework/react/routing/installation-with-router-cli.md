---
source-updated-at: '2025-02-27T00:31:02.000Z'
translation-updated-at: '2025-05-08T20:16:57.368Z'
title: 使用 Router CLI 安裝
---

> [!WARNING]
> 僅在未使用支援的打包工具時才應使用 TanStack Router CLI。CLI 僅支援路由樹檔案的生成，不提供其他功能。

要使用 TanStack Router CLI 進行基於檔案的路由設定，您需要安裝 `@tanstack/router-cli` 套件。

```sh
npm install -D @tanstack/router-cli
```

安裝完成後，您需要在 `package.json` 中修改腳本，以便 CLI 能夠 `watch` 和 `generate` 檔案。

```json
{
  "scripts": {
    "generate-routes": "tsr generate",
    "watch-routes": "tsr watch",
    "build": "npm run generate-routes && ...",
    "dev": "npm run watch-routes && ..."
  }
}
```

[//]: # 'AfterScripts'
[//]: # 'AfterScripts'

請勿忘記 _忽略_ 生成的路由樹檔案。請前往 [忽略生成的路由樹檔案](#ignoring-the-generated-route-tree-file) 章節了解更多資訊。

安裝 CLI 後，可透過 `tsr` 指令使用以下命令：

## 使用 `generate` 命令

根據提供的配置生成專案的路由。

```sh
tsr generate
```

## 使用 `watch` 命令

持續監控指定目錄並在需要時重新生成路由。

**用法：**

```sh
tsr watch
```

啟用基於檔案的路由後，當您在開發模式下啟動應用程式時，TanStack Router 會監控您配置的 `routesDirectory`，並在檔案新增、刪除或變更時生成路由樹。

## 忽略生成的路由樹檔案

如果您的專案配置了 linter 或格式化工具，您可能需要忽略生成的路由樹檔案。此檔案由 TanStack Router 管理，因此不應由您的 linter 或格式化工具修改。

以下是一些幫助您忽略生成路由樹檔案的資源：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，可能會在重新命名路由後意外開啟路由樹檔案（並顯示錯誤）。

您可以透過 VSCode 設定將該檔案標記為唯讀來防止此情況。我們建議同時透過以下設定將其排除在搜尋結果和檔案監控之外：

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

使用 TanStack Router CLI 進行基於檔案的路由設定時，它提供了一些適用於大多數專案的預設值：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果這些預設值適用於您的專案，您無需進行任何配置！但如果您需要自訂配置，可以在專案根目錄建立 `tsr.config.json` 檔案來實現。

[//]: # 'TargetConfiguration'
[//]: # 'TargetConfiguration'

您可以在 [基於檔案的路由 API 參考](../../../api/file-based-routing.md) 中找到所有可用的配置選項。
