---
source-updated-at: '2025-03-10T19:00:21.000Z'
translation-updated-at: '2025-05-08T20:25:41.752Z'
id: path-aliases
title: 路徑別名 (Path Aliases)
---

路徑別名 (Path Aliases) 是 TypeScript 的一項實用功能，可讓你為專案目錄結構中較深層的路徑定義捷徑。這能幫助你避免在程式碼中使用冗長的相對路徑導入 (relative imports)，並讓專案結構重構更加容易。對於避免程式碼中出現冗長的相對路徑導入尤其有用。

預設情況下，TanStack Start 並未包含路徑別名功能。但你可以透過更新專案根目錄中的 `tsconfig.json` 檔案並加入以下設定來輕鬆添加：

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

在此範例中，我們定義了路徑別名 `~/*`，它會映射到 `./src/*` 目錄。這表示你現在可以使用 `~` 前綴來從 `src` 目錄導入檔案。

更新 `tsconfig.json` 檔案後，你需要安裝 `vite-tsconfig-paths` 插件以在 TanStack Start 專案中啟用路徑別名功能。可透過執行以下指令完成：

```sh
npm install -D vite-tsconfig-paths
```

接著，你需要更新 `app.config.ts` 檔案以包含以下內容：

```ts
// app.config.ts
import { defineConfig } from '@tanstack/react-start/config'
import viteTsConfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  vite: {
    plugins: [
      // 這是啟用路徑別名的插件
      viteTsConfigPaths({
        projects: ['./tsconfig.json'],
      }),
    ],
  },
})
```

完成此設定後，你現在就能像這樣使用路徑別名來導入檔案：

```ts
// app/routes/posts/$postId/edit.tsx
import { Input } from '~/components/ui/input'

// 取代原本的

import { Input } from '../../../components/ui/input'
```
