---
source-updated-at: 2025-02-25T23:33:22.000Z
translation-updated-at: 2025-04-05T03:20:02.000Z
title: 使用 Webpack 安装
---

[//]: # 'BundlerConfiguration'

要在 **Webpack** 中使用基于文件的路由，您需要安装 `@tanstack/router-plugin` 包。

```sh
npm install -D @tanstack/router-plugin
```

安装完成后，您需要将该插件添加到配置中。

```tsx
// webpack.config.ts
import { TanStackRouterWebpack } from '@tanstack/router-plugin/webpack'

export default {
  plugins: [
    TanStackRouterWebpack({ target: 'react', autoCodeSplitting: true }),
  ],
}
```

或者，您可以克隆我们的 [Webpack 快速入门示例](https://github.com/TanStack/router/tree/main/examples/react/quickstart-webpack-file-based) 直接开始使用。

现在您已将插件添加到 Webpack 配置中，可以开始使用 TanStack Router 的基于文件路由功能了。

[//]: # 'BundlerConfiguration'

## 忽略生成的路由树文件

如果您的项目配置了代码检查工具和/或格式化工具，您可能需要忽略生成的路由树文件。该文件由 TanStack Router 管理，因此不应被您的检查工具或格式化工具修改。

以下是一些帮助您忽略生成的路由树文件的资源：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，在重命名路由后可能会遇到路由树文件意外打开（并显示错误）的情况。

您可以通过在 VSCode 设置中将该文件标记为只读来防止这种情况。我们还建议通过以下设置将其从搜索结果和文件监视器中排除：

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

您可以在用户级别应用这些设置，也可以通过在项目根目录创建 `.vscode/settings.json` 文件仅对单个工作区生效。

## 配置

当在 Webpack 中使用 TanStack Router 插件进行基于文件的路由时，它提供了一些合理的默认配置，适用于大多数项目：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果这些默认配置适合您的项目，您无需进行任何额外配置！但是，如果需要自定义配置，您可以通过编辑传递给 `TanStackRouterWebpack` 函数的配置对象来实现。

您可以在 [基于文件路由的 API 参考](../../../api/file-based-routing.md) 中找到所有可用的配置选项。
