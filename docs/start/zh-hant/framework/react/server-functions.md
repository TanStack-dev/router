---
source-updated-at: '2025-04-14T17:55:22.000Z'
translation-updated-at: '2025-05-08T20:27:49.133Z'
id: server-functions
title: 伺服器函式 (Server Functions)
---

## 什麼是伺服器函式 (Server Functions)？

伺服器函式允許您指定可在幾乎任何地方（甚至客戶端）呼叫，但**僅**在伺服器上執行的邏輯。實際上，它們與 API 路由 (API Route) 並無太大差異，但有幾個關鍵區別：

- 它們沒有穩定的公開 URL（但您很快就能實現此功能！）
- 可以從應用程式的任何地方呼叫，包括載入器 (loaders)、鉤子 (hooks)、元件 (components) 等，但不能從 API 路由中呼叫。

然而，它們與常規 API 路由的相似之處在於：

- 可以存取請求上下文 (request context)，允許您讀取標頭 (headers)、設定 Cookie 等
- 可以存取敏感資訊，例如環境變數 (environment variables)，而不會將其暴露給客戶端
- 可用於執行任何類型的伺服器端邏輯，例如從資料庫獲取資料、發送電子郵件或與其他服務互動
- 可以返回任何值，包括基本型別 (primitives)、可 JSON 序列化的物件，甚至是原始 Response 物件
- 可以拋出錯誤，包括重新導向 (redirects) 和 notFound，這些錯誤可由路由器 (router) 自動處理

> 伺服器函式與「React 伺服器函式 (React Server Functions)」有何不同？
>
> - TanStack 伺服器函式不依賴於特定的前端框架，可以與任何前端框架或無框架環境一起使用。
> - TanStack 伺服器函式基於標準 HTTP 請求，可以隨意呼叫，而不會因序列執行瓶頸而受到影響。

## 它們如何運作？

伺服器函式可以在應用程式的任何地方定義，但必須在檔案的頂層定義。可以在應用程式的任何地方呼叫，包括載入器、鉤子等。傳統上，這種模式稱為遠端程序呼叫 (Remote Procedure Call, RPC)，但由於這些函式的同構特性 (isomorphic nature)，我們將其稱為伺服器函式。

- 在伺服器套件 (server bundle) 中，伺服器函式的邏輯保持不變。由於它們已經在正確的位置，因此無需進行任何操作。
- 在客戶端，伺服器函式將被移除；它們僅存在於伺服器上。客戶端對伺服器函式的任何呼叫將被替換為向伺服器發送的 `fetch` 請求，以執行伺服器函式並將回應傳回客戶端。

## 伺服器函式中介軟體 (Middleware)

伺服器函式可以使用中介軟體來共享邏輯、上下文、常見操作、先決條件等。要了解更多關於伺服器函式中介軟體的資訊，請務必閱讀[中介軟體指南](./middleware.md)。

## 定義伺服器函式

> 我們要感謝 [tRPC](https://trpc.io/) 團隊，他們不僅為 TanStack Start 的伺服器函式設計提供了靈感，還在實現過程中給予了指導。我們非常喜歡（並推薦）在 API 路由中使用 tRPC，因此我們堅持讓伺服器函式獲得同樣的一流待遇和開發者體驗。謝謝！

伺服器函式使用 `@tanstack/react-start` 套件中的 `createServerFn` 函式定義。此函式接受一個可選的 `options` 參數，用於指定 HTTP 方法和回應類型等配置，並允許您鏈接結果以定義伺服器函式的主體、輸入驗證、中介軟體等。以下是一個簡單範例：

```tsx
// getServerTime.ts
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn().handler(async () => {
  // 等待 1 秒
  await new Promise((resolve) => setTimeout(resolve, 1000))
  // 返回當前時間
  return new Date().toISOString()
})
```

### 配置選項

在建立伺服器函式時，可以提供配置選項以自訂其行為：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getData = createServerFn({
  method: 'GET', // 使用的 HTTP 方法
  response: 'data', // 回應處理模式
}).handler(async () => {
  // 函式實現
})
```

#### 可用選項

**`method`**

指定伺服器函式請求的 HTTP 方法：

```tsx
method?: 'GET' | 'POST'
```

如果未指定，伺服器函式預設使用 `GET`。

**`response`**

控制如何處理和返回回應：

```tsx
response?: 'data' | 'full' | 'raw'
```

- `'data'`（預設）：自動解析 JSON 回應並僅返回資料
- `'full'`：返回包含結果資料、錯誤資訊和上下文的回應物件
- `'raw'`：直接返回原始 Response 物件，支援串流回應和自訂標頭

## 可以在哪裡呼叫伺服器函式？

- 從伺服器端程式碼
- 從客戶端程式碼
- 從其他伺服器函式

> [!WARNING]
> 伺服器函式無法從 API 路由中呼叫。如果需要共享業務邏輯，請將其提取為可被伺服器函式和 API 路由導入的共用工具函式。

## 接受參數

伺服器函式接受單一參數，可以是多種類型：

- 標準 JavaScript 型別
  - `string`
  - `number`
  - `boolean`
  - `null`
  - `Array`
  - `Object`
- FormData
- ReadableStream（上述任何型別的串流）
- Promise（上述任何型別的 Promise）

以下是一個接受簡單字串參數的伺服器函式範例：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const greet = createServerFn({
  method: 'GET',
})
  .validator((data: string) => data)
  .handler(async (ctx) => {
    return `Hello, ${ctx.data}!`
  })

greet({
  data: 'John',
})
```

## 執行時期輸入驗證 / 型別安全

伺服器函式可以配置為在執行時期驗證輸入資料，同時增加型別安全性。這對於確保在執行伺服器函式之前輸入資料的型別正確，並提供更友好的錯誤訊息非常有用。

這是通過 `validator` 方法實現的。它將接受傳遞給伺服器函式的任何輸入。從此函式返回的值（和型別）將成為傳遞給實際伺服器函式處理程序的輸入。

驗證器還可以與外部驗證庫（如 Zod）無縫整合。

### 基本驗證

以下是一個簡單的伺服器函式範例，用於驗證輸入參數：

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown): Person => {
    if (typeof person !== 'object' || person === null) {
      throw new Error('Person must be an object')
    }

    if ('name' in person && typeof person.name !== 'string') {
      throw new Error('Person.name must be a string')
    }

    return person as Person
  })
  .handler(async ({ data }) => {
    return `Hello, ${data.name}!`
  })
```

### 使用驗證庫

可以使用像 Zod 這樣的驗證庫，如下所示：

```tsx
import { createServerFn } from '@tanstack/react-start'

import { z } from 'zod'

const Person = z.object({
  name: z.string(),
})

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown) => {
    return Person.parse(person)
  })
  .handler(async (ctx) => {
    return `Hello, ${ctx.data.name}!`
  })

greet({
  data: {
    name: 'John',
  },
})
```

## 型別安全

由於伺服器函式跨越網路邊界，確保傳遞給它們的資料不僅型別正確，而且在執行時期經過驗證非常重要。這在處理使用者輸入時尤其重要，因為使用者輸入可能不可預測。為了確保開發者驗證其 I/O 資料，型別依賴於驗證。`validator` 函式的返回型別將是伺服器函式處理程序的輸入。

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((person: unknown): Person => {
    if (typeof person !== 'object' || person === null) {
      throw new Error('Person must be an object')
    }

    if ('name' in person && typeof person.name !== 'string') {
      throw new Error('Person.name must be a string')
    }

    return person as Person
  })
  .handler(
    async ({
      data, // Person
    }) => {
      return `Hello, ${data.name}!`
    },
  )

function test() {
  greet({ data: { name: 'John' } }) // OK
  greet({ data: { name: 123 } }) // Error: Argument of type '{ name: number; }' is not assignable to parameter of type 'Person'.
}
```

## 型別推論

伺服器函式根據 `validator` 的輸入和 `handler` 函式的返回值推論其輸入和輸出型別。實際上，您定義的 `validator` 甚至可以有自己的獨立輸入/輸出型別，這在驗證器對輸入資料執行轉換時非常有用。

為了說明這一點，讓我們看一個使用 `zod` 驗證庫的範例：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { z } from 'zod'

const transactionSchema = z.object({
  amount: z.string().transform((val) => parseInt(val, 10)),
})

const createTransaction = createServerFn()
  .validator(transactionSchema)
  .handler(({ data }) => {
    return data.amount // 返回一個數字
  })

createTransaction({
  data: {
    amount: '123', // 接受一個字串
  },
})
```

## 非驗證型別推論

雖然我們強烈建議使用驗證庫來驗證網路 I/O 資料，但您可能出於某種原因**不**想驗證資料，但仍希望具有型別安全性。為此，可以使用身份函式 (identity function) 作為 `validator`，將輸入和/或輸出型別指定為正確的型別：

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
}

export const greet = createServerFn({ method: 'GET' })
  .validator((d: Person) => d)
  .handler(async (ctx) => {
    return `Hello, ${ctx.data.name}!`
  })

greet({
  data: {
    name: 'John',
  },
})
```

## JSON 參數

伺服器函式可以接受可 JSON 序列化的物件作為參數。這對於將複雜的資料結構傳遞給伺服器非常有用：

```tsx
import { createServerFn } from '@tanstack/react-start'

type Person = {
  name: string
  age: number
}

export const greet = createServerFn({ method: 'GET' })
  .validator((data: Person) => data)
  .handler(async ({ data }) => {
    return `Hello, ${data.name}! You are ${data.age} years old.`
  })

greet({
  data: {
    name: 'John',
    age: 34,
  },
})
```

## FormData 參數

伺服器函式可以接受 `FormData` 物件作為參數：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const greetUser = createServerFn({ method: 'POST' })
  .validator((data) => {
    if (!(data instanceof FormData)) {
      throw new Error('Invalid form data')
    }
    const name = data.get('name')
    const age = data.get('age')

    if (!name || !age) {
      throw new Error('Name and age are required')
    }

    return {
      name: name.toString(),
      age: parseInt(age.toString(), 10),
    }
  })
  .handler(async ({ data: { name, age } }) => {
    return `Hello, ${name}! You are ${age} years old.`
  })

// 使用方式
function Test() {
  return (
    <form
      onSubmit={async (event) => {
        event.preventDefault()
        const formData = new FormData(event.currentTarget)
        const response = await greetUser({ data: formData })
        console.log(response)
      }}
    >
      <input name="name" />
      <input name="age" />
      <button type="submit">Submit</button>
    </form>
  )
}
```

## 伺服器函式上下文

除了伺服器函式接受的單一參數外，您還可以使用 `@tanstack/react-start/server` 中的工具從任何伺服器函式中存取伺服器請求上下文。在底層，我們使用 [Unjs](https://unjs.io/) 的 `h3` 套件來執行跨平台的 HTTP 請求。

有許多上下文函式可供您使用，例如：

- 存取請求上下文
- 存取/設定標頭
- 存取/設定會話/Cookie
- 設定回應狀態碼和狀態訊息
- 處理多部分表單資料
- 讀取/設定自訂伺服器上下文屬性

有關可用上下文函式的完整列表，請參閱所有可用的 [h3 方法](https://h3.unjs.io/utils/request) 或檢查 [@tanstack/start-server-core 原始碼](https://github.com/TanStack/router/tree/main/packages/start-server-core/src)。

以下是一些基本範例：

## 存取請求上下文

讓我們使用 `getWebRequest` 函式從伺服器函式中存取請求本身：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { getWebRequest } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    const request = getWebRequest()

    console.log(request.method) // GET

    console.log(request.headers.get('User-Agent')) // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
  },
)
```

## 存取標頭

使用 `getHeaders` 函式從伺服器函式中存取所有標頭：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { getHeaders } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    console.log(getHeaders())
    // {
    //   "accept": "*/*",
    //   "accept-encoding": "gzip, deflate, br",
    //   "accept-language": "en-US,en;q=0.9",
    //   "connection": "keep-alive",
    //   "host": "localhost:3000",
    //   ...
    // }
  },
)
```

您還可以使用 `getHeader` 函式存取單個標頭：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { getHeader } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    console.log(getHeader('User-Agent')) // Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3
  },
)
```

## 返回值

伺服器函式可以返回幾種不同類型的值：

- 基本型別
- 可 JSON 序列化的物件
- `redirect` 錯誤（也可以拋出）
- `notFound` 錯誤（也可以拋出）
- 原始 Response 物件

## 返回基本型別和 JSON

要返回任何基本型別或可 JSON 序列化的物件，只需從伺服器函式中返回值：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    return new Date().toISOString()
  },
)

export const getServerData = createServerFn({ method: 'GET' }).handler(
  async () => {
    return {
      message: 'Hello, World!',
    }
  },
)
```

預設情況下，伺服器函式假定任何非 Response 物件返回的值都是基本型別或可 JSON 序列化的物件。

## 使用自訂標頭回應

要使用自訂標頭回應，可以使用 `setHeader` 函式：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { setHeader } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    setHeader('X-Custom-Header', 'value')
    return new Date().toISOString()
  },
)
```

## 使用自訂狀態碼回應

要使用自訂狀態碼回應，可以使用 `setResponseStatus` 函式：

```tsx
import { createServerFn } from '@tanstack/react-start'
import { setResponseStatus } from '@tanstack/react-start/server'

export const getServerTime = createServerFn({ method: 'GET' }).handler(
  async () => {
    setResponseStatus(201)
    return new Date().toISOString()
  },
)
```

## 返回原始 Response 物件

要返回原始 Response 物件，請從伺服器函式中返回一個 Response 物件並設定 `response: 'raw'`：

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn({
  method: 'GET',
  response: 'raw',
}).handler(async () => {
  // 從 s3 讀取檔案
  return fetch('https://example.com/time.txt')
})
```

`response: 'raw'` 選項還允許串流回應等功能：

```tsx

```
