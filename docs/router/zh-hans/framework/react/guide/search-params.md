---
source-updated-at: 2025-04-01T20:07:41.000Z
translation-updated-at: 2025-04-05T03:26:00.000Z
title: 搜索参数
---

正如 TanStack Query 让处理 React 和 Solid 应用中的服务器状态变得轻而易举，TanStack Router 旨在释放应用中 URL 搜索参数的强大能力。

## 为什么不直接使用 `URLSearchParams`？

我们理解，最近你经常听到“利用平台原生能力”的建议，对此我们大部分情况下表示认同。然而，我们也认为有必要认识到平台在更高级用例中的不足，而 `URLSearchParams` 正是其中之一。

传统的搜索参数 API 通常基于以下假设：

- 搜索参数始终是字符串类型
- 它们**基本**是扁平结构
- 使用 `URLSearchParams` 进行序列化和反序列化已经足够（剧透警告：事实并非如此）
- 搜索参数的修改与 URL 路径名紧密耦合，即使路径名未变化也必须同步更新

但现实与这些假设大相径庭：

- 搜索参数代表应用状态，因此我们自然期望它们能拥有与其他状态管理工具相同的开发体验。这意味着需要能区分原始值类型，并能高效存储和操作复杂数据结构（如嵌套数组和对象）。
- 序列化和反序列化状态有多种方式，各有优劣。你应该能为应用选择最佳方案，或至少获得比 `URLSearchParams` 更好的默认实现。
- 不可变性与结构共享。每次对 URL 搜索参数进行字符串化和解析时，引用完整性和对象标识都会丢失，因为每次解析都会创建全新的数据结构并分配独立内存引用。如果在生命周期内管理不当，这种持续的序列化和解析可能导致意外的性能问题，特别是在像 React 这样通过不可变性跟踪响应性，或像 Solid 这样通常依赖协调机制检测反序列化数据源变化的框架中。
- 搜索参数虽然是 URL 的重要组成部分，但经常独立于 URL 路径名变化。例如用户可能只想更改分页列表的页码而不改动路径名。

## 搜索参数 —— "元老级"状态管理工具

你一定见过类似 `?page=3` 或 `?filter-name=tanner` 的 URL 搜索参数。这无疑是**一种存在于 URL 中的全局状态形式**。将特定状态存储在 URL 中具有重要价值，因为：

- 用户应当能够：
  - 通过 Cmd/Ctrl + 点击在新标签页打开链接，并准确看到预期状态
  - 收藏或分享应用链接，并确保他人看到的状态与复制链接时完全一致
  - 刷新应用或来回导航页面而不丢失状态
- 开发者应当能够轻松：
  - 以与其他状态管理工具同样优秀的开发体验增删改 URL 中的状态
  - 轻松验证来自 URL 的搜索参数，确保其格式和类型对应用安全可靠
  - 读写搜索参数而无需关心底层序列化格式

## JSON 优先的搜索参数

为实现上述目标，TanStack Router 内置的第一步是一个强大的搜索参数解析器，能自动将 URL 查询字符串转换为结构化 JSON。这意味着你可以在搜索参数中存储任何可 JSON 序列化的数据结构，它们会被解析并序列化为 JSON。这相较 `URLSearchParams` 是巨大改进，后者对类数组结构和嵌套数据的支持非常有限。

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

将生成如下 URL：

```
/shop?pageIndex=3&includeCategories=%5B%22electronics%22%2C%22gifts%22%5D&sortBy=price&desc=true
```

当解析此 URL 时，搜索参数会被准确转换回以下 JSON：

```json
{
  "pageIndex": 3,
  "includeCategories": ["electronics", "gifts"],
  "sortBy": "price",
  "desc": true
}
```

你会发现几个关键点：

- 第一层搜索参数保持扁平化和字符串基础，与 `URLSearchParams` 兼容
- 非字符串的第一层值会被准确保留为实际数字和布尔类型
- 嵌套数据结构自动转换为 URL 安全的 JSON 字符串

> 🧠 其他工具通常假设搜索参数始终是扁平且基于字符串的，因此我们选择在第一层保持与 URLSearchParam 兼容。这意味着即使 TanStack Router 以 JSON 形式管理嵌套搜索参数，其他工具仍能正常写入 URL 并读取第一层参数。

## 验证与类型化搜索参数

尽管 TanStack Router 能将搜索参数解析为可靠的 JSON，但它们本质上仍来自**用户输入的原始文本**。与其他序列化边界类似，这意味着在消费搜索参数前，应先验证为应用可信任和依赖的格式。

### 引入验证 + TypeScript！

TanStack Router 提供了便捷的 API 来验证和类型化搜索参数，这一切始于路由的 `validateSearch` 选项：

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

上例中，我们验证路由的搜索参数并返回类型化的 `ProductSearch` 对象。这个类型化对象随后可供该路由的其他选项**及其所有子路由**使用！

### 验证搜索参数

`validateSearch` 选项是一个函数，接收 JSON 解析后（但未验证）的搜索参数作为 `Record<string, unknown>`，并返回你指定的类型化对象。通常最好为格式错误或意外的搜索参数提供合理的回退值，以确保用户体验不受干扰。

以下示例使用 [Zod](https://zod.dev/) 库（当然你也可以选择其他验证库）一步完成搜索参数的验证和类型化：

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

由于 `validateSearch` 也接受包含 `parse` 属性的对象，因此可简写为：

```tsx
validateSearch: productSearchSchema
```

上例中我们使用 Zod 的 `.catch()` 而非 `.default()` 来避免向用户显示错误，因为我们坚信当搜索参数格式错误时，不应中断用户体验来显示巨大错误信息。当然，有时你可能**确实需要显示错误信息**，此时可用 `.default()` 替代 `.catch()`。

这种机制的工作原理依赖于 `validateSearch` 函数抛出错误。若抛出错误，将触发路由的 `onError` 选项（且 `error.routerCode` 设为 `VALIDATE_SEARCH`），并渲染 `errorComponent` 而非路由的 `component`，你可以在其中按需处理搜索参数错误。

#### 适配器

使用 [Zod](https://zod.dev/) 等库验证搜索参数时，可能需要在提交到 URL 前对参数进行 `transform`。常见的 `zod` 转换如 `default`。

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

你可能会惊讶地发现，导航到此路由时 `search` 是必填的。以下 `Link` 会因缺少 `search` 而类型错误：

```tsx
<Link to="/shop/products" />
```

对于验证库，我们推荐使用能推断正确 `input` 和 `output` 类型的适配器。

### Zod

[Zod](https://zod.dev/) 适配器能正确传递 `input` 和 `output` 类型：

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

关键改进是以下 `Link` 不再需要 `search` 参数：

```tsx
<Link to="/shop/products" />
```

但此处使用 `catch` 会覆盖类型并使 `page`、`filter` 和 `sort` 变为 `unknown` 导致类型丢失。我们通过提供 `fallback` 泛型函数解决了这个问题，它能在验证失败时保留类型并提供回退值：

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

因此导航到此路由时，`search` 变为可选且保留正确类型。

虽然不推荐，但也可配置 `input` 和 `output` 类型，当 `output` 类型比 `input` 更精确时：

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

这为导航时推断的类型和读取搜索参数时推断的类型提供了灵活性。

### Valibot

> [!警告]
> 需安装 valibot 1.0 包。

使用 [Valibot](https://valibot.dev/) 时无需适配器即可确保导航和读取搜索参数时使用正确的 `input` 和 `output` 类型，因为 `valibot` 实现了 [Standard Schema](https://github.com/standard-schema/standard-schema)：

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

> [!警告]
> 需安装 arktype 2.0-rc 包。

使用 [ArkType](https://arktype.io/) 时无需适配器，因为它实现了 [Standard Schema](https://github.com/standard-schema/standard-schema)：

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

## Effect/Schema

使用 [Effect/Schema](https://effect.website/docs/schema/introduction/) 时无需适配器，因为它实现了 [Standard Schema](https://github.com/standard-schema/standard-schema)：

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

当搜索参数完成验证和类型化后，就可以开始读写操作了。TanStack Router 提供了多种方式来实现这一点。请阅读[加载器中的搜索参数](./data-loading.md#using-loaderdeps-to-access-search-params)章节，了解如何通过`loaderDeps`选项在加载器中读取搜索参数。

### 搜索参数继承自父路由

搜索参数及其类型会随着路由树的向下传递而合并，因此子路由也能访问其父级的搜索参数：

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

你可以通过`useSearch`钩子在路由的`component`中访问已验证的搜索参数。

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
> 如果你的组件是代码分割的，可以使用[getRouteApi函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)来避免导入`Route`配置，从而访问类型化的`useSearch()`钩子。

### 路由组件外的搜索参数

你可以使用`useSearch`钩子在应用的任何地方访问路由的已验证搜索参数。通过传递源路由的`from` id/路径，你将获得更好的类型安全：

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

或者，你可以通过传递`strict: false`来放宽类型安全，获取一个可选的`search`对象：

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

现在你已经学会了如何读取路由的搜索参数，你会很高兴地发现你已经看到了修改和更新它们的主要API。让我们简单回顾一下。

### `<Link search />`

更新搜索参数的最佳方式是使用`<Link />`组件的`search`属性。

如果要更新当前页面的搜索参数并指定了`from`属性，可以省略`to`属性。  
以下是一个示例：

```tsx
// /routes/shop.products.tsx
export const Route = createFileRoute('/shop/products')({
  validateSearch: productSearchSchema,
})

const ProductList = () => {
  return (
    <div>
      <Link from={Route.fullPath} search={(prev) => ({ page: prev.page + 1 })}>
        下一页
      </Link>
    </div>
  )
}
```

如果你想在一个在多个路由上渲染的通用组件中更新搜索参数，指定`from`可能会有挑战性。

在这种情况下，你可以设置`to="."`，这将允许你访问松散类型的搜索参数。  
以下是一个示例：

```tsx
// `page`是在__root路由中定义的搜索参数，因此在所有路由上都可用。
const PageSelector = () => {
  return (
    <div>
      <Link to="." search={(prev) => ({ ...prev, page: prev.page + 1 })}>
        下一页
      </Link>
    </div>
  )
}
```

如果通用组件仅在路由树的特定子树中渲染，你可以使用`from`指定该子树。在这里，如果你想，可以省略`to='.'`。

```tsx
// `page`是在/posts路由中定义的搜索参数，因此在其所有子路由上都可用。
const PageSelector = () => {
  return (
    <div>
      <Link
        from="/posts"
        to="."
        search={(prev) => ({ ...prev, page: prev.page + 1 })}
      >
        下一页
      </Link>
    </div>
  )
```

### `useNavigate(), navigate({ search })`

`navigate`函数也接受一个`search`选项，其工作方式与`<Link />`上的`search`属性相同：

```tsx
// /routes/shop.products.tsx
export const Route = createFileRoute('/shop/products/$productId')({
  validateSearch: productSearchSchema,
})

const ProductList = () => {
  const navigate = useNavigate({ from: Route.fullPath })

  return (
    <div>
      <button
        onClick={() => {
          navigate({
            search: (prev) => ({ page: prev.page + 1 }),
          })
        }}
      >
        下一页
      </button>
    </div>
  )
}
```

### `router.navigate({ search })`

`router.navigate`函数的工作方式与上述`useNavigate`/`navigate`钩子/函数完全相同。

### `<Navigate search />`

`<Navigate search />`组件的工作方式与上述`useNavigate`/`navigate`钩子/函数完全相同，但接受其选项作为props而不是函数参数。

## 使用搜索中间件转换搜索

当构建链接href时，默认情况下，对于查询字符串部分唯一重要的是`<Link>`的`search`属性。

TanStack Router提供了一种在生成href之前通过**搜索中间件**操作搜索参数的方式。
搜索中间件是在为路由或其子路由生成新链接时转换搜索参数的函数。
它们也在导航后执行搜索验证后执行，以允许操作查询字符串。

以下示例展示了如何确保为**每个**正在构建的链接添加`rootValue`搜索参数（如果它是当前搜索参数的一部分）。如果链接在`search`中指定了`rootValue`，则使用该值构建链接。

```tsx
import { z } from 'zod'
import { createFileRoute } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  rootValue: z.string().optional(),
})

export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema),
  search: {
    middlewares: [
      ({search, next}) => {
        const result = next(search)
        return {
          rootValue: search.rootValue
          ...result
        }
      }
    ]
  }
})
```

由于这种特定用例非常常见，TanStack Router通过`retainSearchParams`提供了一个通用实现来保留搜索参数：

```tsx
import { z } from 'zod'
import { createFileRoute, retainSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const searchSchema = z.object({
  rootValue: z.string().optional(),
})

export const Route = createRootRoute({
  validateSearch: zodValidator(searchSchema),
  search: {
    middlewares: [retainSearchParams(['rootValue'])],
  },
})
```

另一个常见用例是如果搜索参数设置了默认值，则从链接中删除它们。TanStack Router通过`stripSearchParams`为此用例提供了一个通用实现：

```tsx
import { z } from 'zod'
import { createFileRoute, stripSearchParams } from '@tanstack/react-router'
import { zodValidator } from '@tanstack/zod-adapter'

const defaultValues = {
  one: 'abc',
  two: 'xyz',
}

const searchSchema = z.object({
  one: z.string().default(defaultValues.one),
  two: z.string().default(defaultValues.two),
})

export const Route = createFileRoute('/hello')({
  validateSearch: zodValidator(searchSchema),
  search: {
    // 删除默认值
    middlewares: [stripSearchParams(defaultValues)],
  },
})
```

可以链接多个中间件。以下示例展示了如何结合`retainSearchParams`和`stripSearchParams`。

```tsx
import {
  Link,
  createFileRoute,
  retainSearchParams,
  stripSearchParams,
} from '@tanstack/react-router'
import { z } from 'zod'
import { zodValidator } from '@tanstack/zod-adapter'

const defaultValues = ['foo', 'bar']

export const Route = createFileRoute('/search')({
  validateSearch: zodValidator(
    z.object({
      retainMe: z.string().optional(),
      arrayWithDefaults: z.string().array().default(defaultValues),
      required: z.string(),
    }),
  ),
  search: {
    middlewares: [
      retainSearchParams(['retainMe']),
      stripSearchParams({ arrayWithDefaults: defaultValues }),
    ],
  },
})
```
