---
source-updated-at: '2025-05-06T22:56:27.000Z'
translation-updated-at: '2025-05-08T20:19:56.815Z'
title: 搜尋參數
---

如同 TanStack Query 讓處理 React 和 Solid 應用中的伺服器狀態變得輕而易舉，TanStack Router 旨在釋放 URL 搜尋參數 (search params) 在應用中的強大潛力。

> 🧠 如果你使用的是非常舊的瀏覽器，例如 IE11，可能需要為 `URLSearchParams` 使用 polyfill。

## 為什麼不只使用 `URLSearchParams`？

我們理解，最近你聽到了很多「使用平台原生功能」的建議，大多數情況下我們也同意。然而，我們也認為重要的是要認識到平台在更進階的使用場景中的不足之處，而 `URLSearchParams` 正是其中之一。

傳統的搜尋參數 API 通常假設了幾件事：

- 搜尋參數總是字串
- 它們**大部分**是扁平的
- 使用 `URLSearchParams` 進行序列化和反序列化已經足夠好（劇透警告：其實不然）
- 搜尋參數的修改與 URL 的路徑名稱 (pathname) 緊密耦合，即使路徑名稱沒有變化，也必須一起更新。

然而，現實與這些假設大相徑庭：

- 搜尋參數代表應用狀態，因此不可避免地，我們會期望它們擁有與其他狀態管理工具相同的開發體驗 (DX)。這意味著能夠區分原始值類型，並高效儲存和操作複雜的資料結構，如嵌套陣列和物件。
- 有許多方法可以序列化和反序列化狀態，各有不同的權衡。你應該能夠選擇最適合應用的一種，或至少獲得比 `URLSearchParams` 更好的預設選項。
- 不可變性與結構共享。每次你將 URL 搜尋參數字串化並解析時，參照完整性和物件識別都會丟失，因為每次新的解析都會創建一個全新的資料結構，具有唯一的記憶體參照。如果在生命週期中沒有妥善管理，這種持續的序列化和解析可能會導致意外和不希望的效能問題，尤其是在像 React 這樣通過不可變性來追蹤反應性，或像 Solid 這樣通常依賴於從反序列化資料源檢測變化的框架中。
- 搜尋參數雖然是 URL 的重要部分，但經常獨立於 URL 的路徑名稱變化。例如，用戶可能希望更改分頁列表的頁碼，而不觸碰 URL 的路徑名稱。

## 搜尋參數，「元老級」狀態管理工具

你可能在 URL 中見過像 `?page=3` 或 `?filter-name=tanner` 這樣的搜尋參數。毫無疑問，這確實是**一種全域狀態**，存在於 URL 中。將特定狀態儲存在 URL 中很有價值，因為：

- 用戶應該能夠：
  - 使用 Cmd/Ctrl + 點擊在新標籤頁中打開連結，並可靠地看到他們預期的狀態
  - 從你的應用中書籤和分享連結給他人，並確保他們看到的狀態與連結複製時完全一致
  - 刷新應用或在頁面之間來回導航而不丟失狀態
- 開發者應該能夠輕鬆地：
  - 添加、刪除或修改 URL 中的狀態，並享有與其他狀態管理工具相同的優秀開發體驗
  - 輕鬆驗證來自 URL 的搜尋參數，確保其格式和類型對應用來說是安全可用的
  - 讀寫搜尋參數，而無需擔心底層的序列化格式

## JSON 優先的搜尋參數

為了實現上述目標，TanStack Router 內建的第一步是一個強大的搜尋參數解析器，能自動將 URL 的搜尋字串轉換為結構化的 JSON。這意味著你可以將任何可 JSON 序列化的資料結構儲存在搜尋參數中，並以 JSON 的形式解析和序列化。這相較於 `URLSearchParams` 是一個巨大的改進，後者對陣列類結構和嵌套資料的支持有限。

例如，導航到以下路由：

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

將生成以下 URL：

```
/shop?pageIndex=3&includeCategories=%5B%22electronics%22%2C%22gifts%22%5D&sortBy=price&desc=true
```

當此 URL 被解析時，搜尋參數將準確轉換回以下 JSON：

```json
{
  "pageIndex": 3,
  "includeCategories": ["electronics", "gifts"],
  "sortBy": "price",
  "desc": true
}
```

如果你注意到了，這裡有幾點值得說明：

- 搜尋參數的第一層是扁平且基於字串的，就像 `URLSearchParams` 一樣。
- 第一層的非字串值會被準確保留為實際的數字和布林值。
- 嵌套的資料結構會自動轉換為 URL 安全的 JSON 字串

> 🧠 其他工具通常假設搜尋參數總是扁平且基於字串的，這就是為什麼我們選擇在第一層保持與 URLSearchParam 兼容。這最終意味著，即使 TanStack Router 將你的嵌套搜尋參數作為 JSON 管理，其他工具仍然能夠正常寫入 URL 並讀取第一層參數。

## 驗證與類型化搜尋參數

儘管 TanStack Router 能夠將搜尋參數解析為可靠的 JSON，但它們最終仍來自**用戶輸入的原始文字**。與其他序列化邊界類似，這意味著在消費搜尋參數之前，應將其驗證為應用可以信任和依賴的格式。

### 引入驗證 + TypeScript！

TanStack Router 提供了方便的 API 來驗證和類型化搜尋參數。這一切都始於 `Route` 的 `validateSearch` 選項：

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
    // 驗證並將搜尋參數解析為類型化狀態
    return {
      page: Number(search?.page ?? 1),
      filter: (search.filter as string) || '',
      sort: (search.sort as ProductSearchSortOptions) || 'newest',
    }
  },
})
```

在上面的例子中，我們驗證了 `Route` 的搜尋參數並返回了一個類型化的 `ProductSearch` 物件。這個類型化物件隨後可供此路由的其他選項**及其任何子路由**使用！

### 驗證搜尋參數

`validateSearch` 選項是一個函數，它接收 JSON 解析（但未驗證）的搜尋參數作為 `Record<string, unknown>`，並返回你選擇的類型化物件。通常最好為格式錯誤或意外的搜尋參數提供合理的回退值，以確保用戶體驗不受干擾。

以下是一個例子：

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
    // 驗證並將搜尋參數解析為類型化狀態
    return {
      page: Number(search?.page ?? 1),
      filter: (search.filter as string) || '',
      sort: (search.sort as ProductSearchSortOptions) || 'newest',
    }
  },
})
```

以下是一個使用 [Zod](https://zod.dev/) 庫（但你也可以使用任何你喜歡的驗證庫）來同時驗證和類型化搜尋參數的例子：

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

因為 `validateSearch` 也接受一個帶有 `parse` 屬性的物件，所以可以簡化為：

```tsx
validateSearch: productSearchSchema
```

在上面的例子中，我們使用了 Zod 的 `.catch()` 修飾符而不是 `.default()`，以避免向用戶顯示錯誤。因為我們堅信，如果搜尋參數格式錯誤，你可能不希望為了顯示一個大大的錯誤訊息而中斷用戶在應用中的體驗。話雖如此，有時你可能**確實希望顯示錯誤訊息**。在這種情況下，你可以使用 `.default()` 而不是 `.catch()`。

這種運作方式的底層機制依賴於 `validateSearch` 函數拋出錯誤。如果拋出錯誤，路由的 `onError` 選項將被觸發（並且 `error.routerCode` 將設置為 `VALIDATE_SEARCH`），`errorComponent` 將被渲染，而不是路由的 `component`，你可以在這裡以任何你喜歡的方式處理搜尋參數錯誤。

#### 適配器

當使用像 [Zod](https://zod.dev/) 這樣的庫來驗證搜尋參數時，你可能希望在將搜尋參數提交到 URL 之前對其進行 `transform`。一個常見的 `zod` `transform` 例子是 `default`。

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

可能會讓你驚訝的是，當你嘗試導航到這個路由時，`search` 是必需的。以下 `Link` 會因為缺少 `search` 而出現類型錯誤。

```tsx
<Link to="/shop/products" />
```

對於驗證庫，我們推薦使用適配器，這些適配器會推斷正確的 `input` 和 `output` 類型。

### Zod

為 [Zod](https://zod.dev/) 提供了一個適配器，它會傳遞正確的 `input` 類型和 `output` 類型

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

這裡的重要部分是，以下 `Link` 的使用不再需要 `search` 參數

```tsx
<Link to="/shop/products" />
```

然而，這裡使用 `catch` 會覆蓋類型並使 `page`、`filter` 和 `sort` 變為 `unknown`，導致類型丟失。我們通過提供一個 `fallback` 泛型函數來處理這種情況，該函數保留了類型，但在驗證失敗時提供了一個 `fallback` 值

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

因此，當導航到這個路由時，`search` 是可選的，並且保留了正確的類型。

雖然不推薦，但也可以配置 `input` 和 `output` 類型，以防 `output` 類型比 `input` 類型更準確

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

這提供了靈活性，可以根據需要推斷導航的類型和讀取搜尋參數的類型。

### Valibot

> [!WARNING]
> 路由器需要安裝 valibot 1.0 套件。

當使用 [Valibot](https://valibot.dev/) 時，不需要適配器來確保導航和讀取搜尋參數時使用正確的 `input` 和 `output` 類型。這是因為 `valibot` 實現了 [Standard Schema](https://github.com/standard-schema/standard-schema)

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
> 路由器需要安裝 arktype 2.0-rc 套件。

當使用 [ArkType](https://arktype.io/) 時，不需要適配器來確保導航和讀取搜尋參數時使用正確的 `input` 和 `output` 類型。這是因為 [ArkType](https://arktype.io/) 實現了 [Standard Schema](https://github.com/standard-schema/standard-schema)

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

當使用 [Effect/Schema](https://effect.website/docs/schema/introduction/) 時，不需要適配器來確保導航和讀取搜尋參數時使用正確的 `input` 和 `output` 類型。這是因為 [Effect/Schema](https://effect.website/docs/schema/standard-schema/) 實現了 [Standard Schema](https://github.com/standard-schema/standard-schema)

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

## 讀取搜尋參數

一旦你的搜尋參數經過驗證和類型化，你終於可以開始讀取和寫入它們了。在 TanStack Router 中有幾種方法可以做到這一點，讓我們來看看。

### 在加載器中使用搜尋參數

請閱讀 [在加載器中使用搜尋參數](./data-loading.md#using-loaderdeps-to-access-search-params) 部分，了解更多關於如何使用 `loaderDeps` 選項在加載器中讀取搜尋參數的信息。

### 搜尋參數從父路由繼承

搜尋參數和類型會隨著你沿著路由樹向下移動而合併，因此子路由也可以訪問其父路由的搜尋參數：

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

### 在元件中使用搜尋參數

你可以通過 `useSearch` 鉤子在路由的 `component` 中訪問經過驗證的搜尋參數。

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

>
