---
source-updated-at: '2025-05-06T22:56:27.000Z'
translation-updated-at: '2025-05-06T23:31:05.825Z'
title: 搜索参数
---

# 搜索参数 (Search Params)

正如 TanStack Query 让处理 React 和 Solid 应用中的服务端状态 (server-state) 变得轻而易举一样，TanStack Router 旨在释放应用中 URL 搜索参数 (search params) 的强大功能。

> 🧠 如果您使用的是非常旧的浏览器（如 IE11），可能需要为 `URLSearchParams` 使用 polyfill。

## 为什么不直接使用 `URLSearchParams`？

我们理解，最近您可能经常听到"使用平台原生 API"的建议，在大多数情况下我们认同。然而，我们也认为认识到平台在更高级用例中的不足很重要，而 `URLSearchParams` 正是这种情况之一。

传统的搜索参数 API 通常假设以下几点：

- 搜索参数始终是字符串
- 它们*大部分*是扁平的
- 使用 `URLSearchParams` 进行序列化和反序列化已经足够好（剧透警告：事实并非如此）
- 搜索参数的修改与 URL 的路径名 (pathname) 紧密耦合，即使路径名没有变化也必须一起更新

然而，现实与这些假设大不相同：

- 搜索参数代表应用状态，因此我们不可避免地期望它们能拥有与其他状态管理器相同的开发者体验 (DX)。这意味着需要能够区分原始值类型，并高效存储和操作复杂数据结构（如嵌套数组和对象）。
- 有多种序列化和反序列化状态的方法，各有不同的权衡。您应该能为应用选择最佳方案，或者至少获得比 `URLSearchParams` 更好的默认选项。
- 不可变性与结构共享 (Immutability & Structural Sharing)。每次字符串化和解析 URL 搜索参数时，引用完整性和对象标识都会丢失，因为每个新的解析都会创建一个具有唯一内存引用的全新数据结构。如果在其生命周期内管理不当，这种持续的序列化和解析可能会导致意外和不希望的性能问题，特别是在像 React 这样通过不可变性跟踪响应性，或像 Solid 这样通常依赖协调 (reconciliation) 来检测反序列化数据源变化的框架中。
- 搜索参数虽然是 URL 的重要组成部分，但经常独立于 URL 的路径名变化。例如，用户可能希望更改分页列表的页码而不触及 URL 的路径名。

## 搜索参数 —— "元老级"状态管理器

您可能已经在 URL 中见过类似 `?page=3` 或 `?filter-name=tanner` 的搜索参数。毫无疑问，这确实是**一种存在于 URL 中的全局状态形式**。将特定状态片段存储在 URL 中很有价值，因为：

- 用户应该能够：
  - 使用 Cmd/Ctrl + 点击在新标签页中打开链接，并可靠地看到他们期望的状态
  - 从您的应用中收藏和分享链接给他人，并确保他们看到的状态与链接被复制时完全一致
  - 刷新应用或在页面间前后导航而不丢失状态
- 开发者应该能够轻松地：
  - 以与其他状态管理器相同的优秀开发者体验 (DX) 添加、删除或修改 URL 中的状态
  - 轻松验证来自 URL 的搜索参数，确保其格式和类型对应用来说是安全可用的
  - 读写搜索参数而不必担心底层的序列化格式

## 优先 JSON 的搜索参数

为实现上述目标，TanStack Router 内置的第一步是一个强大的搜索参数解析器，能自动将 URL 的查询字符串转换为结构化 JSON。这意味着您可以在搜索参数中存储任何可 JSON 序列化的数据结构，它将被解析并序列化为 JSON。这相比 `URLSearchParams` 是一个巨大改进，后者对类数组结构和嵌套数据的支持有限。

例如，导航到以下路由：

```tsx
const link = (
  <Link
    to="/shop"
    search={{
      pageIndex: 3,
      includeCategories: ['electronics', 'gifts'],
      sortBy: 'price',
      desc: true,
    }}
  />
)
```

将生成以下 URL：

```
/shop?pageIndex=3&includeCategories=%5B%22electronics%22%2C%22gifts%22%5D&sortBy=price&desc=true
```

当解析此 URL 时，搜索参数将被准确转换回以下 JSON：

```json
{
  "pageIndex": 3,
  "includeCategories": ["electronics", "gifts"],
  "sortBy": "price",
  "desc": true
}
```

如果您注意到了，这里有几件事：

- 搜索参数的第一层是扁平且基于字符串的，就像 `URLSearchParams` 一样
- 第一层中非字符串的值被准确保留为实际的数字和布尔值
- 嵌套数据结构会自动转换为 URL 安全的 JSON 字符串

> 🧠 其他工具通常假设搜索参数始终是扁平且基于字符串的，这就是为什么我们选择在第一层保持与 URLSearchParam 兼容。这最终意味着，即使 TanStack Router 将您的嵌套搜索参数作为 JSON 管理，其他工具仍能正常写入 URL 并读取第一层参数。

## 验证和类型化搜索参数

尽管 TanStack Router 能够将搜索参数解析为可靠的 JSON，但它们最终仍来自**用户输入的原始文本**。与其他序列化边界类似，这意味着在您使用搜索参数之前，应该将它们验证为应用可以信任和依赖的格式。

### 引入验证 + TypeScript！

TanStack Router 提供了方便的 API 来验证和类型化搜索参数。这一切始于 `Route` 的 `validateSearch` 选项：

```tsx
// /routes/shop.products.tsx

type ProductSearchSortOptions = 'newest' | 'oldest' | 'price'

type ProductSearch = {
  page: number
  filter: string
  sort: ProductSearchSortOptions
}

export const Route = createFileRoute('/shop/products')({
  validateSearch: (search: Record<string, unknown>): ProductSearch => {
    // 验证并将搜索参数解析为类型化状态
    return {
      page: Number(search?.page ?? 1),
      filter: (search.filter as string) || '',
      sort: (search.sort as ProductSearchSortOptions) || 'newest',
    }
  },
})
```

在上面的示例中，我们验证了 `Route` 的搜索参数并返回了一个类型化的 `ProductSearch` 对象。然后，这个类型化对象可用于此路由的其他选项**以及任何子路由！**

### 验证搜索参数

`validateSearch` 选项是一个函数，它接收 JSON 解析（但未验证）的搜索参数作为 `Record<string, unknown>`，并返回您选择的类型化对象。通常最好为格式错误或意外的搜索参数提供合理的回退值，以保持用户体验不受干扰。

以下是一个示例：

```tsx
// /routes/shop.products.tsx

type ProductSearchSortOptions = 'newest' | 'oldest' | 'price'

type ProductSearch = {
  page: number
  filter: string
  sort: ProductSearchSortOptions
}

export const Route = createFileRoute('/shop/products')({
  validateSearch: (search: Record<string, unknown>): ProductSearch => {
    // 验证并将搜索参数解析为类型化状态
    return {
      page: Number(search?.page ?? 1),
      filter: (search.filter as string) || '',
      sort: (search.sort as ProductSearchSortOptions) || 'newest',
    }
  },
})
```

以下是使用 [Zod](https://zod.dev/) 库（但您可以使用任何验证库）在单个步骤中验证和类型化搜索参数的示例：

```tsx
// /routes/shop.products.tsx

import { z } from 'zod'

const productSearchSchema = z.object({
  page: z.number().catch(1),
  filter: z.string().catch(''),
  sort: z.enum(['newest', 'oldest', 'price']).catch('newest'),
})

type ProductSearch = z.infer<typeof productSearchSchema>

export const Route = createFileRoute('/shop/products')({
  validateSearch: (search) => productSearchSchema.parse(search),
})
```

因为 `validateSearch` 也接受带有 `parse` 属性的对象，所以可以简化为：

```tsx
validateSearch: productSearchSchema
```

在上面的示例中，我们使用了 Zod 的 `.catch()` 修饰符而不是 `.default()` 来避免向用户显示错误，因为我们坚信如果搜索参数格式错误，您可能不希望中断用户在应用中的体验来显示一个大大的错误消息。也就是说，有时您**确实想显示错误消息**。在这种情况下，可以使用 `.default()` 而不是 `.catch()`。

其底层机制依赖于 `validateSearch` 函数抛出错误。如果抛出错误，将触发路由的 `onError` 选项（并且 `error.routerCode` 将被设置为 `VALIDATE_SEARCH`），`errorComponent` 将代替路由的 `component` 渲染，您可以在其中根据需要处理搜索参数错误。

#### 适配器

当使用像 [Zod](https://zod.dev/) 这样的库来验证搜索参数时，您可能希望在将搜索参数提交到 URL 之前对其进行 `transform`。例如，常见的 `zod` `transform` 是 `default`。

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { z } from 'zod'

const productSearchSchema = z.object({
  page: z.number().default(1),
  filter: z.string().default(''),
  sort: z.enum(['newest', 'oldest', 'price']).default('newest'),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```

可能会令人惊讶的是，当您尝试导航到此路由时，`search` 是必需的。以下 `Link` 会类型错误，因为缺少 `search`。

```tsx
<Link to="/shop/products" />
```

对于验证库，我们推荐使用适配器，它能推断正确的 `input` 和 `output` 类型。

### Zod

为 [Zod](https://zod.dev/) 提供了一个适配器，它将传递正确的 `input` 和 `output` 类型

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const productSearchSchema = z.object({
  page: z.number().default(1),
  filter: z.string().default(''),
  sort: z.enum(['newest', 'oldest', 'price']).default('newest'),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: zodValidator(productSearchSchema),
})
```

这里的重要部分是以下 `Link` 的使用不再需要 `search` 参数

```tsx
<Link to="/shop/products" />
```

然而，这里的 `catch` 使用会覆盖类型并使 `page`、`filter` 和 `sort` 变为 `unknown`，导致类型丢失。我们通过提供一个 `fallback` 泛型函数来处理这种情况，它保留了类型但在验证失败时提供了 `fallback` 值

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { fallback, zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const productSearchSchema = z.object({
  page: fallback(z.number(), 1).default(1),
  filter: fallback(z.string(), '').default(''),
  sort: fallback(z.enum(['newest', 'oldest', 'price']), 'newest').default(
    'newest',
  ),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: zodValidator(productSearchSchema),
})
```

因此，当导航到此路由时，`search` 是可选的并保留了正确的类型。

虽然不推荐，但也可以配置 `input` 和 `output` 类型，以防 `output` 类型比 `input` 类型更准确

```tsx
const productSearchSchema = z.object({
  page: fallback(z.number(), 1).default(1),
  filter: fallback(z.string(), '').default(''),
  sort: fallback(z.enum(['newest', 'oldest', 'price']), 'newest').default(
    'newest',
  ),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: zodValidator({
    schema: productSearchSchema,
    input: 'output',
    output: 'input',
  }),
})
```

这为导航时想要推断的类型和读取搜索参数时想要推断的类型提供了灵活性。

### Valibot

> [!WARNING]
> 路由器需要安装 valibot 1.0 包。

当使用 [Valibot](https://valibot.dev/) 时，不需要适配器来确保导航和读取搜索参数时使用正确的 `input` 和 `output` 类型。这是因为 `valibot` 实现了 [Standard Schema](https://github.com/standard-schema/standard-schema)

```tsx
import { createFileRoute } from '@tanstack/react-router'
import * as v from 'valibot'

const productSearchSchema = v.object({
  page: v.optional(v.fallback(v.number(), 1), 1),
  filter: v.optional(v.fallback(v.string(), ''), ''),
  sort: v.optional(
    v.fallback(v.picklist(['newest', 'oldest', 'price']), 'newest'),
    'newest',
  ),
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```

### Arktype

> [!WARNING]
> 路由器需要安装 arktype 2.0-rc 包。

当使用 [ArkType](https://arktype.io/) 时，不需要适配器来确保导航和读取搜索参数时使用正确的 `input` 和 `output` 类型。这是因为 [ArkType](https://arktype.io/) 实现了 [Standard Schema](https://github.com/standard-schema/standard-schema)

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { type } from 'arktype'

const productSearchSchema = type({
  page: 'number = 1',
  filter: 'string = ""',
  sort: '"newest" | "oldest" | "price" = "newest"',
})

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```

### Effect/Schema

当使用 [Effect/Schema](https://effect.website/docs/schema/introduction/) 时，不需要适配器来确保导航和读取搜索参数时使用正确的 `input` 和 `output` 类型。这是因为 [Effect/Schema](https://effect.website/docs/schema/standard-schema/) 实现了 [Standard Schema](https://github.com/standard-schema/standard-schema)

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { Schema as S } from 'effect'

const productSearchSchema = S.standardSchemaV1(
  S.Struct({
    page: S.NumberFromString.pipe(
      S.optional,
      S.withDefaults({
        constructor: () => 1,
        decoding: () => 1,
      }),
    ),
    filter: S.String.pipe(
      S.optional,
      S.withDefaults({
        constructor: () => '',
        decoding: () => '',
      }),
    ),
    sort: S.Literal('newest', 'oldest', 'price').pipe(
      S.optional,
      S.withDefaults({
        constructor: () => 'newest' as const,
        decoding: () => 'newest' as const,
      }),
    ),
  }),
)

export const Route = createFileRoute('/shop/products/')({
  validateSearch: productSearchSchema,
})
```

## 读取搜索参数

一旦您的搜索参数经过验证和类型化，您终于可以开始读写它们了。TanStack Router 中有几种方法可以做到这一点，让我们来看看。

### 在加载器中使用搜索参数

请阅读[加载器中的搜索参数](./data-loading.md#using-loaderdeps-to-access-search-params)部分，了解如何使用 `loaderDeps` 选项在加载器中读取搜索参数的更多信息。

### 搜索参数从父路由继承

搜索参数和类型会随着您沿着路由树向下移动而合并，因此子路由也可以访问其父路由的搜索参数：

- `shop.products.tsx`

```tsx
const productSearchSchema = z.object({
  page: z.number().catch(1),
  filter: z.string().catch(''),
  sort: z.enum(['newest', 'oldest', 'price']).catch('newest'),
})

type ProductSearch = z.infer<typeof productSearchSchema>

export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
})
```

- `shop.products.$productId.tsx`

```tsx
export const Route = createFileRoute('/shop/products/$productId')({
  beforeLoad: ({ search }) => {
    search
    // ^? ProductSearch ✅
  },
})
```

### 组件中的搜索参数

您可以通过 `useSearch` 钩子在路由的 `component` 中访问经过验证的搜索参数。

```tsx
// /routes/shop.products.tsx

export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
})

const ProductList = () => {
  const { page, filter, sort } = Route.useSearch()

  return <div>...</div>
}
```

> [!TIP]
> 如果您的组件是代码分割的，可以使用 [getRouteApi 函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)来避免必须导入 `Route` 配置以获取类型化的 `useSearch()` 钩子。

### 路由组件外的搜索参数

您可以使用 `useSearch` 钩子在应用的任何位置访问路由的验证过的搜索参数。通过传递源路由的 `from` id/path，您将获得更好的类型安全：

```tsx
// /routes/shop.products.tsx
export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
  // ...
})

// 其他位置...

// /components/product-list-sidebar.tsx
const routeApi = getRouteApi('/shop/products')

const ProductList = () => {
  const routeSearch = routeApi.useSearch()

  // 或

  const { page, filter, sort } = useSearch({
    from: Route.fullPath,
  })

  return <div>...</div>
}
```

或者，您可以通过传递 `strict: false` 来放宽类型安全并获取可选的 `search` 对象：

```tsx
function ProductList() {
  const search = useSearch({
    strict: false,
  })
  // {
  //   page: number | undefined
  //   filter: string | undefined
  //   sort: 'newest' | 'oldest' | 'price' | undefined
  // }

  return <div>...</div>
}
```

## 写入搜索参数

现在您已经
