---
source-updated-at: 2025-03-07T21:06:39.000Z
translation-updated-at: 2025-04-05T03:38:00.000Z
title: 基于文件的路由
---

TanStack Router 基于文件的路由系统非常灵活，可根据项目需求进行配置。

## 配置选项

以下是可用于配置基于文件路由的选项：

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
> 请勿将 `routeFilePrefix`、`routeFileIgnorePrefix` 或 `routeFileIgnorePattern` 选项设置为与**文件命名约定**指南中使用的任何标记相匹配，否则可能导致意外行为。

### `routesDirectory` (必填)

这是路由文件所在目录的路径，相对于当前工作目录（cwd）。

默认值设置为以下内容，且不能设置为空 `string` 或 `undefined`。

```txt
./src/routes
```

### `generatedRouteTree` (必填)

这是生成的路径树将保存到的文件的路径，相对于当前工作目录（cwd）。

默认值设置为以下内容，且不能设置为空 `string` 或 `undefined`。

```txt
./src/routeTree.gen.ts
```

如果 [`disableTypes`](#disabletypes) 设置为 `true`，生成的路径树将以 `.js` 扩展名保存，而不是 `.ts`。

### `virtualRouteConfig`

此选项用于配置虚拟文件路由功能。更多信息请参阅“虚拟文件路由”指南。

默认情况下，此值设置为 `undefined`。

### `routeFileIgnorePrefix`

此选项用于忽略路由目录中的特定文件和目录。如果您希望“选择加入”某些不希望用于路由的文件或目录，这会很有用。

默认情况下，此值设置为 `-`。

使用此选项时，您可以拥有如下结构，其中可以放置与路由文件无关的相关文件：

```txt
src/routes
├── posts
│   ├── -components  // 被忽略
│   │   ├── Post.tsx
│   ├── index.tsx
│   ├── route.tsx
```

### `routeFileIgnorePattern`

此选项用于忽略路由目录中的特定文件和目录。可以使用正则表达式格式。例如，`.((css|const).ts)|test-page` 将忽略名称包含 `.css.ts`、`.const.ts` 或 `test-page` 的文件/目录。

默认情况下，此值设置为 `undefined`。

### `routeFilePrefix`

此选项用于标识路由目录中的路由文件。这意味着只有以此前缀开头的文件才会被视为路由文件。

默认情况下，此值设置为空字符串，因此路由目录中的所有文件都将被视为路由文件。

### `routeToken`

如路由概念指南中所述，布局路由在指定路径渲染，子路由在布局路由内渲染。`routeToken` 用于标识路由目录中的布局路由文件。

默认情况下，此值设置为 `route`。

> 🧠 以下文件名将对应相同的运行时 URL：

```txt
src/routes/posts.tsx -> /posts
src/routes/posts.route.tsx -> /posts
src/routes/posts/route.tsx -> /posts
```

### `indexToken`

如路由概念指南中所述，索引路由是当 URL 路径与父路由完全匹配时匹配的路由。`indexToken` 用于标识路由目录中的索引路由文件。

默认情况下，此值设置为 `index`。

> 🧠 以下文件名将对应相同的运行时 URL：

```txt
src/routes/posts.index.tsx -> /posts/
src/routes/posts/index.tsx -> /posts/
```

### `quoteStyle`

当生成的路径树生成或首次创建新路由时，这些文件将按照此处指定的引号样式格式化。

默认情况下，此值设置为 `single`。

> [!TIP]
> 您应该将生成的路径树文件的路径从 linter 和格式化工具中忽略，以避免冲突。

### `semicolons`

当生成的路径树生成或首次创建新路由时，如果此选项设置为 `true`，这些文件将使用分号格式化。

默认情况下，此值设置为 `false`。

> [!TIP]
> 您应该将生成的路径树文件的路径从 linter 和格式化工具中忽略，以避免冲突。

### `apiBase`

作为框架，[TanStack Start](/start) 支持 API 路由的概念。此选项配置 API 路由的基础路径。

默认情况下，此值设置为 `/api`。

这意味着所有 API 路由都将以 `/api` 为前缀。

此配置值仅在您使用 TanStack Start 时有用。

> [!IMPORTANT]
> 如果您计划拥有相同基础路径的普通路由，此默认值可能与您项目的路由冲突。您可以更改此值以避免冲突。

### `autoCodeSplitting`

此功能仅在您使用 TanStack Router Bundler Plugin 时可用。

此选项用于为非关键路由配置项启用自动代码拆分。更多信息请参阅“自动代码拆分”指南。

默认情况下，此值设置为 `false`。

> [!IMPORTANT]
> TanStack Router 的下一个主要版本（即 v2）将默认此值为 `true`。

### `disableTypes`

此选项用于禁用生成路径树的类型。

如果设置为 `true`，生成的路径树将不包含任何类型，并将作为 `.js` 文件而不是 `.ts` 文件写入。

默认情况下，此值设置为 `false`。

### `addExtensions`

此选项将文件扩展名添加到生成的路径树中的路由名称中。

默认情况下，此值设置为 `false`。

### `disableLogging`

此选项关闭路由生成过程的控制台日志记录。

默认情况下，此值设置为 `false`。

### `routeTreeFileHeader`

此选项允许您在生成的路径树文件的开头添加内容。

默认情况下，此值设置为：

```json
[
  "/* eslint-disable */",
  "// @ts-nocheck",
  "// noinspection JSUnusedGlobalSymbols"
]
```

### `routeTreeFileFooter`

此选项允许您在生成的路径树文件的末尾追加内容。

默认情况下，此值设置为：

```json
[]
```

### `disableManifestGeneration`

[TanStack Start](/start) 利用 `generatedRouteTree` 文件存储 JSON 树，使 Start 能够轻松遍历可用的路径树以了解应用程序的路由结构。此 JSON 树保存在生成的路径树文件的末尾。

此选项允许您禁用清单的生成。

默认情况下，此值设置为 `false`。

### `enableRouteTreeFormatting`

此选项开启生成的路径树文件的格式化功能，对于大型项目可能会耗时。

默认情况下，此值设置为 `true`。
