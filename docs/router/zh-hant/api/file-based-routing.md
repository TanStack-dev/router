---
source-updated-at: '2025-03-07T21:06:39.000Z'
translation-updated-at: '2025-05-08T20:15:41.399Z'
title: 基於檔案的路由
---

TanStack Router 的檔案式路由 (file-based routing) 具有高度靈活性，可根據專案需求進行配置。

## 配置選項

以下選項可用於配置檔案式路由：

- [`routesDirectory` (必填)](#routesdirectory-required)
- [`generatedRouteTree` (必填)](#generatedroutetree-required)
- [`virtualRouteConfig`](#virtualrouteconfig)
- [`routeFilePrefix`](#routefileprefix)
- [`routeFileIgnorePrefix`](#routefileignoreprefix)
- [`indexToken`](#indextoken)
- [`routeToken`](#routetoken)
- [`quoteStyle`](#quotestyle)
- [`semicolons`](#semicolons)
- [`apiBase`](#apibase)
- [`autoCodeSplitting`](#autocodesplitting)
- [`disableTypes`](#disabletypes)
- [`addExtensions`](#addextensions)
- [`disableLogging`](#disablelogging)
- [`routeTreeFileHeader`](#routetreefileheader)
- [`routeTreeFileFooter`](#routetreefilefooter)
- [`disableManifestGeneration`](#disablemanifestgeneration)
- [`enableRouteTreeFormatting`](#enableroutetreeformatting)

> [!WARNING]
> 請勿將 `routeFilePrefix`、`routeFileIgnorePrefix` 或 `routeFileIgnorePattern` 選項設定為與 **檔案命名慣例** 指南中使用的標記相同，否則可能導致非預期行為。

### `routesDirectory` (必填)

此為路由檔案所在目錄的路徑，相對於當前工作目錄 (cwd)。

預設值設定如下，且不可設為空 `string` 或 `undefined`。

```txt
./src/routes
```

### `generatedRouteTree` (必填)

此為生成的路由樹將保存的檔案路徑，相對於當前工作目錄 (cwd)。

預設值設定如下，且不可設為空 `string` 或 `undefined`。

```txt
./src/routeTree.gen.ts
```

若 [`disableTypes`](#disabletypes) 設為 `true`，生成的路由樹將以 `.js` 副檔名保存，而非 `.ts`。

### `virtualRouteConfig`

此選項用於配置虛擬檔案路由 (Virtual File Routes) 功能。詳見「虛擬檔案路由」指南。

預設值為 `undefined`。

### `routeFileIgnorePrefix`

此選項用於忽略路由目錄中的特定檔案和目錄。適用於「選擇性排除」某些不需用於路由的檔案或目錄。

預設值設為 `-`。

使用此選項時，可建立如下結構，將非路由的相關檔案集中存放：

```txt
src/routes
├── posts
│   ├── -components  // 被忽略
│   │   ├── Post.tsx
│   ├── index.tsx
│   ├── route.tsx
```

### `routeFileIgnorePattern`

此選項用於以正則表達式格式忽略路由目錄中的特定檔案和目錄。例如，`.((css|const).ts)|test-page` 將忽略名稱包含 `.css.ts`、`.const.ts` 或 `test-page` 的檔案/目錄。

預設值為 `undefined`。

### `routeFilePrefix`

此選項用於識別路由目錄中的路由檔案。僅以此前綴開頭的檔案會被視為路由檔案。

預設值為空，因此路由目錄中的所有檔案都會被視為路由檔案。

### `routeToken`

如路由概念指南所述，布局路由 (layout route) 會在指定路徑渲染，子路由則在布局路由內渲染。`routeToken` 用於識別路由目錄中的布局路由檔案。

預設值設為 `route`。

> 🧠 以下檔案名稱會對應到相同的執行階段 URL：

```txt
src/routes/posts.tsx -> /posts
src/routes/posts.route.tsx -> /posts
src/routes/posts/route.tsx -> /posts
```

### `indexToken`

如路由概念指南所述，索引路由 (index route) 會在 URL 路徑與父路由完全相同時匹配。`indexToken` 用於識別路由目錄中的索引路由檔案。

預設值設為 `index`。

> 🧠 以下檔案名稱會對應到相同的執行階段 URL：

```txt
src/routes/posts.index.tsx -> /posts/
src/routes/posts/index.tsx -> /posts/
```

### `quoteStyle`

生成路由樹檔案及建立新路由時，將使用此處指定的引號風格進行格式化。

預設值設為 `single`。

> [!TIP]
> 建議將生成的路由樹檔案路徑從 linter 和 formatter 中排除，以避免衝突。

### `semicolons`

若設為 `true`，生成路由樹檔案及建立新路由時，將在語句結尾添加分號。

預設值設為 `false`。

> [!TIP]
> 建議將生成的路由樹檔案路徑從 linter 和 formatter 中排除，以避免衝突。

### `apiBase`

作為框架，[TanStack Start](/start) 支援 API 路由概念。此選項用於配置 API 路由的基礎路徑。

預設值設為 `/api`。

這意味著所有 API 路由都會以 `/api` 為前綴。

此配置僅在使用 TanStack Start 時有用。

> [!IMPORTANT]
> 若您計劃使用相同基礎路徑的一般路由，此預設值可能與專案路由衝突。可更改此值以避免衝突。

### `autoCodeSplitting`

此功能僅在使用 TanStack Router Bundler Plugin 時可用。

此選項用於啟用非關鍵路由配置項目的自動程式碼分割 (code-splitting)。詳見「自動程式碼分割」指南。

預設值設為 `false`。

> [!IMPORTANT]
> TanStack Router 的下個主要版本 (即 v2) 將預設此值為 `true`。

### `disableTypes`

此選項用於停用路由樹的類型生成。

若設為 `true`，生成的路由樹將不包含任何類型，並以 `.js` 檔案而非 `.ts` 檔案形式寫入。

預設值設為 `false`。

### `addExtensions`

此選項會在生成的路由樹中為路由名稱添加副檔名。

預設值設為 `false`。

### `disableLogging`

此選項會關閉路由生成過程的控制台日誌記錄。

預設值設為 `false`。

### `routeTreeFileHeader`

此選項可讓您在生成的路由樹檔案開頭添加內容。

預設值設為：

```json
[
  "/* eslint-disable */",
  "// @ts-nocheck",
  "// noinspection JSUnusedGlobalSymbols"
]
```

### `routeTreeFileFooter`

此選項可讓您在生成的路由樹檔案結尾附加內容。

預設值設為：

```json
[]
```

### `disableManifestGeneration`

[TanStack Start](/start) 利用 `generatedRouteTree` 檔案來儲存 JSON 樹，使 Start 能輕鬆遍歷可用路由樹以理解應用程式的路由結構。此 JSON 樹會保存在生成的路由樹檔案末尾。

此選項可停用清單 (manifest) 的生成。

預設值設為 `false`。

### `enableRouteTreeFormatting`

此選項會啟用生成路由樹檔案的格式化功能，對於大型專案可能耗時較長。

預設值設為 `true`。
