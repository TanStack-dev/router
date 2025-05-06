---
source-updated-at: 2025-03-10T19:00:21.000Z
translation-updated-at: 2025-04-05T03:43:07.000Z
title: 路径别名
id: path-aliases
---

路径别名是 TypeScript 的一项实用功能，它允许您为项目中可能位于深层目录结构的路径定义快捷方式。这能帮助您避免代码中出现冗长的相对导入，并使项目结构重构更加容易。该功能对于消除代码中冗长的相对导入特别有用。

默认情况下，TanStack Start 不包含路径别名功能。但您可以通过更新项目根目录下的 `tsconfig.json` 文件并添加以下配置来轻松实现：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "~/*": ["./src/*"]
    }
  }
}
```

在此示例中，我们定义了路径别名 `~/*` 映射到 `./src/*` 目录。这意味着您现在可以使用 `~` 前缀从 `src` 目录导入文件。

更新 `tsconfig.json` 文件后，您需要安装 `vite-tsconfig-paths` 插件以在 TanStack Start 项目中启用路径别名功能。可通过运行以下命令完成：

```sh
npm install -D vite-tsconfig-paths
```

接下来，您需要更新 `app.config.ts` 文件以包含以下内容：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'
import viteTsConfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  vite: {
    plugins: [
      // 这是启用路径别名的插件
      viteTsConfigPaths({
        projects: ['./tsconfig.json'],
      }),
    ],
  },
})
```

完成此配置后，您现在可以像这样使用路径别名导入文件：

```ts
// app/routes/posts/$postId/edit.tsx
import { Input } from '~/components/ui/input'

// 替代原先的

import { Input } from '../../../components/ui/input'
```
