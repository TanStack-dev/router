---
source-updated-at: '2025-04-30T06:35:26.000Z'
translation-updated-at: '2025-05-08T20:28:41.837Z'
id: api-routes
title: API 路由 (API Routes)
---

API 路由 (API Routes) 是 TanStack Start 的一項強大功能，讓您無需額外伺服器即可在應用程式中建立伺服器端端點。API 路由 (API Routes) 適用於處理表單提交、使用者驗證等情境。

預設情況下，API 路由 (API Routes) 定義在專案的 `./app/routes/api` 目錄中，並由 TanStack Start 伺服器自動處理。

> 🧠 這表示預設情況下，您的 API 路由 (API Routes) 會以 `/api` 為前綴，並與應用程式由同一台伺服器提供服務。您可以透過修改 TanStack Start 配置中的 `tsr.apiBase` 來自訂此基礎路徑。

## 檔案路由慣例

TanStack Start 中的 API 路由 (API Routes) 遵循與 TanStack Router 相同的檔案式路由慣例。這表示在 `routes` 目錄中，所有以 `api` 為前綴的檔案（可配置）都會被視為 API 路由 (API Routes)。以下是一些範例：

- `routes/api.users.ts` 會建立位於 `/api/users` 的 API 路由 (API Route)
- `routes/api/users.ts` **同樣**會建立位於 `/api/users` 的 API 路由 (API Route)
- `routes/api/users.index.ts` **同樣**會建立位於 `/api/users` 的 API 路由 (API Route)
- `routes/api/users/$id.ts` 會建立位於 `/api/users/$id` 的 API 路由 (API Route)
- `routes/api/users/$id/posts.ts` 會建立位於 `/api/users/$id/posts` 的 API 路由 (API Route)
- `routes/api.users.$id.posts.ts` **同樣**會建立位於 `/api/users/$id/posts` 的 API 路由 (API Route)
- `routes/api/file/$.ts` 會建立位於 `/api/file/$` 的 API 路由 (API Route)

以 `api` 為前綴的路由檔案，可視為對應 API 路由路徑的處理器。

請注意，每條路由只能有一個對應的處理器檔案。因此，若您有名為 `routes/api/users.ts` 的檔案（對應請求路徑 `/api/users`），就不能有其他解析至相同路由的檔案，例如：

- `routes/api/users.index.ts`
- `routes/api.users.ts`
- `routes/api.users.index.ts`

❗ 還有一點，API 路由 (API Routes) 不支援無路徑佈局路由 (pathless layout routes) 或平行路由 (parallel routes)。因此，名為：

- `routes/api/_pathlessLayout/users.ts` 的檔案會解析為 `/api/_pathlessLayout/users`，而**非** `/api/users`。

## 巢狀目錄 vs 檔案命名

在上述範例中，您可能注意到檔案命名慣例具有彈性，允許混用目錄與檔名。這是刻意設計的，讓您能依應用程式需求組織 API 路由 (API Routes)。更多資訊請參閱 [TanStack Router 檔案式路由指南](/router/latest/docs/framework/react/routing/file-based-routing#s-or-s)。

## 設定入口處理器

在建立 API 路由 (API Routes) 前，您需要為 TanStack Start 專案設定入口處理器。此入口處理器類似 `client` 和 `ssr`，負責處理傳入的 API 請求並路由至對應的 API 路由處理器。API 入口處理器定義在專案的 `app/api.ts` 檔案中。

以下是一個實作範例：

```ts
// app/api.ts
import {
  createStartAPIHandler,
  defaultAPIFileRouteHandler,
} from '@tanstack/react-start/api'

export default createStartAPIHandler(defaultAPIFileRouteHandler)
```

此檔案負責建立 API 處理器，用於將傳入請求路由至適當的 API 路由處理器。`defaultAPIFileRouteHandler` 是一個輔助函式，會根據傳入請求自動載入並執行對應的 API 路由處理器。

## 定義 API 路由

API 路由 (API Routes) 透過呼叫 `createAPIFileRoute` 函式來匯出一個 APIRoute 實例。與 TanStack Router 中的其他檔案式路由類似，此函式的第一個參數是路由路徑。回傳的函式會再次被呼叫，並傳入一個定義各 HTTP 方法處理器的物件。

> [!TIP]
> 若您已啟動開發伺服器，建立新 API 路由 (API Route) 時會自動為您設定初始處理器。之後您可依需求自訂處理器。

> [!NOTE]
> 匯出的變數必須命名為 `APIRoute`，否則回傳的響應會是 `404 not found`。

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  GET: async ({ request }) => {
    return new Response('Hello, World! from ' + request.url)
  },
})
```

每個 HTTP 方法處理器會接收一個包含以下屬性的物件：

- `request`: 傳入的請求物件。更多關於 `Request` 物件的資訊請參閱 [MDN Web 文件](https://developer.mozilla.org/en-US/docs/Web/API/Request)。
- `params`: 包含路由動態路徑參數的物件。例如，若路由路徑是 `/users/$id`，且請求發送至 `/users/123`，則 `params` 會是 `{ id: '123' }`。本指南稍後會介紹動態路徑參數和萬用參數。

處理請求後，您需要回傳一個 `Response` 物件或 `Promise<Response>`。這可透過建立新的 `Response` 物件並從處理器回傳來實現。更多關於 `Response` 物件的資訊請參閱 [MDN Web 文件](https://developer.mozilla.org/en-US/docs/Web/API/Response)。

## 動態路徑參數

API 路由 (API Routes) 支援動態路徑參數，以 `$` 加上參數名稱表示。例如，名為 `routes/api/users/$id.ts` 的檔案會建立一個位於 `/api/users/$id` 的 API 路由 (API Route)，接受動態 `id` 參數。

```ts
// routes/api/users/$id.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/users/$id')({
  GET: async ({ params }) => {
    const { id } = params
    return new Response(`User ID: ${id}`)
  },
})

// 訪問 /api/users/123 查看響應
// User ID: 123
```

您也可以在單一路由中使用多個動態路徑參數。例如，名為 `routes/api/users/$id/posts/$postId.ts` 的檔案會建立一個位於 `/api/users/$id/posts/$postId` 的 API 路由 (API Route)，接受兩個動態參數。

```ts
// routes/api/users/$id/posts/$postId.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/users/$id/posts/$postId')({
  GET: async ({ params }) => {
    const { id, postId } = params
    return new Response(`User ID: ${id}, Post ID: ${postId}`)
  },
})

// 訪問 /api/users/123/posts/456 查看響應
// User ID: 123, Post ID: 456
```

## 萬用/展開參數

API 路由 (API Routes) 也支援路徑結尾的萬用參數，以單獨的 `$` 表示。例如，名為 `routes/api/file/$.ts` 的檔案會建立一個位於 `/api/file/$` 的 API 路由 (API Route)，接受萬用參數。

```ts
// routes/api/file/$.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/file/$')({
  GET: async ({ params }) => {
    const { _splat } = params
    return new Response(`File: ${_splat}`)
  },
})

// 訪問 /api/file/hello.txt 查看響應
// File: hello.txt
```

## 處理帶有主體的請求

要處理 POST 請求，您可以在路由物件中加入 `POST` 處理器。處理器會接收請求物件作為第一個參數，並可使用 `request.json()` 方法存取請求主體。

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  POST: async ({ request }) => {
    const body = await request.json()
    return new Response(`Hello, ${body.name}!`)
  },
})

// 發送 POST 請求至 /api/hello 並附上 JSON 主體如 { "name": "Tanner" }
// Hello, Tanner!
```

這也適用於其他 HTTP 方法如 `PUT`、`PATCH` 和 `DELETE`。您可以在路由物件中為這些方法加入處理器，並使用適當的方法存取請求主體。

請注意，`request.json()` 方法會回傳一個解析為請求 JSON 主體的 `Promise`。您需要 `await` 結果才能存取主體。

這是處理 API 路由 (API Routes) 中 POST 請求的常見模式。您也可以使用其他方法如 `request.text()` 或 `request.formData()` 來存取請求主體。

## 回傳 JSON 響應

使用 Response 物件回傳 JSON 時，常見模式如下：

```ts
// routes/api/hello.ts
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  GET: async ({ request }) => {
    return new Response(JSON.stringify({ message: 'Hello, World!' }), {
      headers: {
        'Content-Type': 'application/json',
      },
    })
  },
})

// 訪問 /api/hello 查看響應
// {"message":"Hello, World!"}
```

## 使用 `json` 輔助函式

或者，您可以使用 `json` 輔助函式自動設定 `Content-Type` 標頭為 `application/json` 並為您序列化 JSON 物件。

```ts
// routes/api/hello.ts
import { json } from '@tanstack/react-start'
import { createAPIFileRoute } from '@tanstack/react-start/api'

export const APIRoute = createAPIFileRoute('/api/hello')({
  GET: async ({ request }) => {
    return json({ message: 'Hello, World!' })
  },
})

// 訪問 /api/hello 查看響應
// {"message":"Hello, World!"}
```

## 回傳狀態碼

您可以透過以下方式設定響應的狀態碼：

- 將其作為第二個參數的屬性傳遞給 `Response` 建構函式

  ```ts
  // routes/api/hello.ts
  import { json } from '@tanstack/react-start'
  import { createAPIFileRoute } from '@tanstack/react-start/api'

  export const APIRoute = createAPIFileRoute('/users/$id')({
    GET: async ({ request, params }) => {
      const user = await findUser(params.id)
      if (!user) {
        return new Response('User not found', {
          status: 404,
        })
      }
      return json(user)
    },
  })
  ```

- 使用 `@tanstack/react-start/server` 中的 `setResponseStatus` 輔助函式

  ```ts
  // routes/api/hello.ts
  import { json } from '@tanstack/react-start'
  import { createAPIFileRoute } from '@tanstack/react-start/api'
  import { setResponseStatus } from '@tanstack/react-start/server'

  export const APIRoute = createAPIFileRoute('/users/$id')({
    GET: async ({ request, params }) => {
      const user = await findUser(params.id)
      if (!user) {
        setResponseStatus(404)
        return new Response('User not found')
      }
      return json(user)
    },
  })
  ```

在此範例中，若找不到使用者，我們會回傳 `404` 狀態碼。您可以使用此方法設定任何有效的 HTTP 狀態碼。

## 設定響應標頭

有時您可能需要在響應中設定標頭。您可以透過以下方式實現：

- 將物件作為第二個參數傳遞給 `Response` 建構函式

  ```ts
  // routes/api/hello.ts
  import { createAPIFileRoute } from '@tanstack/react-start/api'

  export const APIRoute = createAPIFileRoute('/api/hello')({
    GET: async ({ request }) => {
      return new Response('Hello, World!', {
        headers: {
          'Content-Type': 'text/plain',
        },
      })
    },
  })

  // 訪問 /api/hello 查看響應
  // Hello, World!
  ```

- 或使用 `@tanstack/react-start/server` 中的 `setHeaders` 輔助函式

  ```ts
  // routes/api/hello.ts
  import { createAPIFileRoute } from '@tanstack/react-start/api'
  import { setHeaders } from '@tanstack/react-start/server'

  export const APIRoute = createAPIFileRoute('/api/hello')({
    GET: async ({ request }) => {
      setHeaders({
        'Content-Type': 'text/plain',
      })
      return new Response('Hello, World!')
    },
  })
  ```
