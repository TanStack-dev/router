---
source-updated-at: 2025-02-27T00:31:02.000Z
translation-updated-at: 2025-04-05T03:20:52.000Z
title: 使用路由器命令行工具安装
---

> [!WARNING]
> 仅当您未使用受支持的打包工具时，才应使用 TanStack Router CLI。该 CLI 仅支持生成路由树文件，不提供其他功能。

要使用基于文件的路由功能，您需要安装 `@tanstack/router-cli` 包。

```sh
npm install -D @tanstack/router-cli
```

安装完成后，您需要修改 `package.json` 中的脚本配置，以便 CLI 能够 `watch`（监听）和 `generate`（生成）文件。

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

请务必记得*忽略*生成的路由树文件。前往 [忽略生成的路由树文件](#ignoring-the-generated-route-tree-file) 部分了解更多信息。

安装 CLI 后，您可以通过 `tsr` 命令使用以下功能：

## 使用 `generate` 命令

根据配置生成项目的路由文件。

```sh
tsr generate
```

## 使用 `watch` 命令

持续监听指定目录，并在需要时重新生成路由。

**用法：**

```sh
tsr watch
```

启用基于文件的路由后，当您在开发模式下启动应用时，TanStack Router 会监听配置的 `routesDirectory` 目录，并在文件添加、删除或修改时自动生成路由树。

## 忽略生成的路由树文件

如果您的项目配置了代码检查工具（linter）或格式化工具，建议忽略生成的路由树文件。该文件由 TanStack Router 管理，因此不应被您的检查工具或格式化工具修改。

以下资源可帮助您忽略该文件：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，在重命名路由后可能会遇到路由树文件意外打开（并显示错误）的情况。

您可以通过 VSCode 设置将该文件标记为只读来避免此问题。我们还建议通过以下设置将其从搜索结果和文件监听中排除：

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

您可以在用户级别或项目级别应用这些设置。对于项目级别，请在项目根目录创建 `.vscode/settings.json` 文件。

## 配置

使用 TanStack Router CLI 进行基于文件的路由时，它提供了一些合理的默认配置，适用于大多数项目：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果这些默认配置适合您的项目，您无需进行任何额外配置！如需自定义配置，可以在项目根目录创建 `tsr.config.json` 文件。

[//]: # 'TargetConfiguration'
[//]: # 'TargetConfiguration'

所有可用的配置选项请参阅 [基于文件的路由 API 参考](../../../api/file-based-routing.md)。
