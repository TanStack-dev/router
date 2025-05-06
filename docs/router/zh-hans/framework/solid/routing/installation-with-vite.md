---
source-updated-at: '2025-02-27T01:26:11.000Z'
translation-updated-at: '2025-05-06T22:01:33.486Z'
title: 使用 Vite 安装
---

要在 **Vite** 中使用基于文件的路由 (file-based routing)，你需要安装 `@tanstack/router-plugin` 包。

```sh
npm install -D @tanstack/router-plugin
```

安装完成后，需要将该插件添加到你的 Vite 配置中。

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

如果使用 TypeScript，还应在 `tsconfig.json` 中添加以下配置：

```json
{
  "compilerOptions": {
    "jsx": "preserve",
    "jsxImportSource": "solid-js"
  }
}
```

或者，你可以直接克隆我们的 [Vite 快速入门示例](https://github.com/TanStack/router/tree/main/examples/solid/quickstart-file-based) 开始使用。

现在你已将插件添加到 Vite 配置中，可以开始使用 TanStack Router 的基于文件路由功能了。

## 忽略生成的路由树文件

如果你的项目配置了代码检查工具 (linter) 或格式化工具，可能需要忽略生成的路由树文件。该文件由 TanStack Router 管理，因此不应被你的检查工具或格式化工具修改。

以下资源可帮助你忽略生成的路由树文件：

- Prettier - [https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore](https://prettier.io/docs/en/ignore.html#ignoring-files-prettierignore)
- ESLint - [https://eslint.org/docs/latest/use/configure/ignore#ignoring-files](https://eslint.org/docs/latest/use/configure/ignore#ignoring-files)
- Biome - [https://biomejs.dev/reference/configuration/#filesignore](https://biomejs.dev/reference/configuration/#filesignore)

> [!WARNING]
> 如果使用 VSCode，在重命名路由后可能会意外打开路由树文件（并显示错误）。

可以通过 VSCode 设置将该文件标记为只读来防止此情况。我们还建议通过以下设置将其从搜索结果和文件监视器中排除：

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

你可以在用户级别或单个工作区中应用这些设置，只需在项目根目录创建 `.vscode/settings.json` 文件即可。

## 配置

当在 Vite 中使用 TanStack Router 插件进行基于文件的路由时，插件提供了一些适用于大多数项目的合理默认配置：

```json
{
  "routesDirectory": "./src/routes",
  "generatedRouteTree": "./src/routeTree.gen.ts",
  "routeFileIgnorePrefix": "-",
  "quoteStyle": "single"
}
```

如果这些默认配置适合你的项目，则无需进行任何额外配置！但如需自定义配置，可以通过编辑传递给 `TanStackRouterVite` 函数的配置对象来实现。

所有可用的配置选项可在 [基于文件路由的 API 参考](../../../api/file-based-routing.md) 中找到。
