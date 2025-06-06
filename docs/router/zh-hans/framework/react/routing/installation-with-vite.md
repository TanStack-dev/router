---
source-updated-at: '2025-04-13T21:10:42.000Z'
translation-updated-at: '2025-05-06T22:01:35.692Z'
title: 使用 Vite 安装
---

[//]: # 'BundlerConfiguration'

要在 **Vite** 中使用基于文件的路由 (file-based routing)，您需要安装 `@tanstack/router-plugin` 包。

```sh
npm install -D @tanstack/router-plugin
```

安装完成后，需要将该插件添加到您的 Vite 配置中。

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    // 请确保 '@tanstack/router-plugin' 在 '@vitejs/plugin-react' 之前传递
    TanStackRouterVite({ target: 'react', autoCodeSplitting: true }),
    react(),
    // ...
  ],
})
```

或者，您可以克隆我们的 [Vite 快速入门示例](https://github.com/TanStack/router/tree/main/examples/react/quickstart-file-based) 直接开始。

> [!WARNING]
> 如果您正在使用旧版的 `@tanstack/router-vite-plugin` 包，仍然可以继续使用它，因为它会被别名映射到 `@tanstack/router-plugin/vite` 包。但我们建议直接使用 `@tanstack/router-plugin` 包。

现在您已将插件添加到 Vite 配置中，可以开始使用 TanStack Router 的基于文件的路由功能了。

[//]: # 'BundlerConfiguration'

## 忽略生成的路由树文件

如果您的项目配置了代码检查工具 (linter) 和/或格式化工具，您可能需要忽略生成的路由树文件。该文件由 TanStack Router 管理，因此不应被您的检查工具或格式化工具修改。

以下是一些帮助您忽略生成路由树文件的资源：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果您使用 VSCode，在重命名路由后可能会遇到路由树文件意外打开（并显示错误）的情况。

您可以通过 VSCode 设置将该文件标记为只读来防止这种情况。我们还建议通过以下设置将其从搜索结果和文件监视器中排除：

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

您可以在用户级别或单个工作区中使用这些设置，只需在项目根目录创建 `.vscode/settings.json` 文件即可。

## 配置

当将 TanStack Router 插件与 Vite 一起用于基于文件的路由时，它附带了一些合理的默认配置，适用于大多数项目：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果这些默认配置适合您的项目，您无需进行任何配置！但如果您需要自定义配置，可以通过编辑传递给 `TanStackRouterVite` 函数的配置对象来实现。

您可以在 [基于文件的路由 API 参考](../../../api/file-based-routing.md) 中找到所有可用的配置选项。
