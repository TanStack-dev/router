---
source-updated-at: '2025-04-26T19:05:15.000Z'
translation-updated-at: '2025-05-08T20:28:27.273Z'
id: middleware
title: 中介軟體 (Middleware)
---

## 什麼是伺服器函式中介軟體 (Server Function Middleware)？

中介軟體 (Middleware) 讓你能透過共享驗證、上下文等功能，自訂由 `createServerFn` 建立的伺服器函式行為。中介軟體甚至可以依賴其他中介軟體，建立一個按層級順序執行的操作鏈。

## 我能在伺服器函式中用中介軟體做哪些事？

- **身份驗證 (Authentication)**：在執行伺服器函式前驗證使用者身份。
- **授權檢查 (Authorization)**：檢查使用者是否有執行伺服器函式的必要權限。
- **日誌記錄 (Logging)**：記錄請求、回應和錯誤。
- **可觀測性 (Observability)**：收集指標、追蹤和日誌。
- **提供上下文 (Provide Context)**：將資料附加到請求物件，供其他中介軟體或伺服器函式使用。
- **錯誤處理 (Error Handling)**：以一致的方式處理錯誤。
- 還有更多！可能性由你決定！

## 為伺服器函式定義中介軟體

中介軟體是透過 `createMiddleware` 函式定義的。此函式會回傳一個 `Middleware` 物件，可用於透過 `middleware`、`validator`、`server` 和 `client` 等方法繼續自訂中介軟體。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next, data }) => {
  console.log('Request received:', data)
  const result = await next()
  console.log('Response processed:', result)
  return result
})
```

## 在伺服器函式中使用中介軟體

定義中介軟體後，你可以將其與 `createServerFn` 函式結合使用，以自訂伺服器函式的行為。

```tsx
import { createServerFn } from '@tanstack/react-start'
import { loggingMiddleware } from './middleware'

const fn = createServerFn()
  .middleware([loggingMiddleware])
  .handler(async () => {
    // ...
  })
```

## 中介軟體方法

有多種方法可用於自訂中介軟體。如果你（希望）使用 TypeScript，這些方法的順序會由型別系統強制執行，以確保最大的推論和型別安全性。

- `middleware`：將中介軟體加入鏈中。
- `validator`：在資料物件傳遞給此中介軟體和任何嵌套中介軟體之前修改它。
- `server`：定義中介軟體在執行任何嵌套中介軟體和最終的伺服器函式之前將執行的伺服器端邏輯，並將結果提供給下一個中介軟體。
- `client`：定義中介軟體在執行任何嵌套中介軟體和最終的客戶端 RPC 函式（或伺服器端函式）之前將執行的客戶端邏輯，並將結果提供給下一個中介軟體。

## `middleware` 方法

`middleware` 方法用於將依賴的中介軟體加入鏈中，這些中介軟體會在**當前中介軟體之前**執行。只需使用中介軟體物件陣列呼叫 `middleware` 方法即可。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().middleware([
  authMiddleware,
  loggingMiddleware,
])
```

型別安全的上下文和負載驗證也會從父中介軟體繼承！

## `validator` 方法

`validator` 方法用於在資料物件傳遞給此中介軟體、嵌套中介軟體和最終的伺服器函式之前修改它。此方法應接收一個函式，該函式接受資料物件並回傳一個經過驗證（可選修改）的資料物件。通常會使用像 `zod` 這樣的驗證函式庫來完成此操作。以下是一個範例：

```tsx
import { createMiddleware } from '@tanstack/react-start'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const mySchema = z.object({
  workspaceId: z.string(),
})

const workspaceMiddleware = createMiddleware()
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('Workspace ID:', data.workspaceId)
    return next()
  })
```

## `server` 方法

`server` 方法用於定義中介軟體在執行任何嵌套中介軟體和最終的伺服器函式之前和之後將執行的**伺服器端**邏輯。此方法接收一個具有以下屬性的物件：

- `next`：一個函式，呼叫時會執行鏈中的下一個中介軟體。
- `data`：傳遞給伺服器函式的資料物件。
- `context`：一個儲存來自父中介軟體資料的物件。可以擴展它，將額外資料傳遞給子中介軟體。

## 從 `next` 回傳所需的結果

`next` 函式用於執行鏈中的下一個中介軟體。**你必須等待並回傳（或直接回傳）提供給你的 `next` 函式的結果**，以便鏈繼續執行。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next }) => {
  console.log('Request received')
  const result = await next()
  console.log('Response processed')
  return result
})
```

## 透過 `next` 提供上下文給下一個中介軟體

`next` 函式可以選擇性地呼叫一個具有 `context` 屬性的物件，該屬性值為物件。你傳遞給此 `context` 值的任何屬性都會合併到父 `context` 中，並提供給下一個中介軟體。

```tsx
import { createMiddleware } from '@tanstack/react-start'

const awesomeMiddleware = createMiddleware().server(({ next }) => {
  return next({
    context: {
      isAwesome: Math.random() > 0.5,
    },
  })
})

const loggingMiddleware = createMiddleware().server(
  async ({ next, context }) => {
    console.log('Is awesome?', context.isAwesome)
    return next()
  },
)
```

## 客戶端邏輯 (Client-Side Logic)

儘管伺服器函式主要是伺服器端綁定的操作，但客戶端發出的 RPC 請求周圍仍有大量客戶端邏輯。這意味著我們也可以在中介軟體中定義客戶端邏輯，這些邏輯將在客戶端執行，圍繞任何嵌套中介軟體和最終的 RPC 函式及其對客戶端的回應。

## 客戶端負載驗證 (Client-side Payload Validation)

預設情況下，中介軟體驗證僅在伺服器端執行，以保持客戶端套件體積小。但是，你也可以選擇透過將 `validateClient: true` 選項傳遞給 `createMiddleware` 函式來在客戶端驗證資料。這將導致資料在發送到伺服器之前在客戶端進行驗證，可能節省一次來回。

> 為什麼我不能為客戶端傳遞不同的驗證架構？
>
> 客戶端驗證架構是從伺服器端架構派生的。這是因為客戶端驗證架構用於在資料發送到伺服器之前驗證資料。如果客戶端架構與伺服器端架構不同，伺服器將收到未預期的資料，這可能導致意外行為。

```tsx
import { createMiddleware } from '@tanstack/react-start'
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const workspaceMiddleware = createMiddleware({ validateClient: true })
  .validator(zodValidator(mySchema))
  .server(({ next, data }) => {
    console.log('Workspace ID:', data.workspaceId)
    return next()
  })
```

## `client` 方法

客戶端中介軟體邏輯是使用 `Middleware` 物件上的 `client` 方法定義的。此方法用於定義客戶端邏輯，中介軟體將在執行任何嵌套中介軟體和最終的客戶端 RPC 函式（或如果你正在進行 SSR 或從另一個伺服器函式呼叫此函式時的伺服器端函式）之前和之後執行。

**客戶端中介軟體邏輯與使用 `server` 方法建立的邏輯共享大部分相同的 API，但它是在客戶端執行的。** 這包括：

- 要求呼叫 `next` 函式以繼續鏈。
- 透過 `next` 函式提供上下文給下一個客戶端中介軟體的能力。
- 在資料物件傳遞給下一個客戶端中介軟體之前修改它的能力。

與 `server` 函式類似，它也接收一個具有以下屬性的物件：

- `next`：一個函式，呼叫時會執行鏈中的下一個客戶端中介軟體。
- `data`：傳遞給客戶端函式的資料物件。
- `context`：一個儲存來自父中介軟體資料的物件。可以擴展它，將額外資料傳遞給子中介軟體。

```tsx
const loggingMiddleware = createMiddleware().client(async ({ next }) => {
  console.log('Request sent')
  const result = await next()
  console.log('Response received')
  return result
})
```

## 將客戶端上下文發送到伺服器

**預設情況下，客戶端上下文不會發送到伺服器，因為這可能最終無意中將大量負載發送到伺服器。** 如果你需要將客戶端上下文發送到伺服器，你必須使用 `sendContext` 屬性和物件呼叫 `next` 函式，以傳輸任何資料到伺服器。傳遞給 `sendContext` 的任何屬性都會被合併、序列化並與資料一起發送到伺服器，並且將在任何嵌套伺服器中介軟體的普通上下文物件上可用。

```tsx
const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        // 將工作區 ID 發送到伺服器
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 哇！我們有來自客戶端的工作區 ID！
    console.log('Workspace ID:', context.workspaceId)
    return next()
  })
```

## 客戶端發送的上下文安全性 (Client-Sent Context Security)

你可能已經注意到，在上面的範例中，雖然客戶端發送的上下文是型別安全的，但它不需要在運行時進行驗證。如果你透過上下文傳遞動態使用者生成的資料，這可能會帶來安全問題，因此**如果你透過上下文從客戶端向伺服器發送動態資料，你應該在伺服器端中介軟體中使用之前驗證它。** 以下是一個範例：

```tsx
import { zodValidator } from '@tanstack/zod-adapter'
import { z } from 'zod'

const requestLogger = createMiddleware()
  .client(async ({ next, context }) => {
    return next({
      sendContext: {
        workspaceId: context.workspaceId,
      },
    })
  })
  .server(async ({ next, data, context }) => {
    // 在使用前驗證工作區 ID
    const workspaceId = zodValidator(z.number()).parse(context.workspaceId)
    console.log('Workspace ID:', workspaceId)
    return next()
  })
```

## 將伺服器上下文發送到客戶端

與將客戶端上下文發送到伺服器類似，你也可以透過使用 `sendContext` 屬性和物件呼叫 `next` 函式，將伺服器上下文發送到客戶端。傳遞給 `sendContext` 的任何屬性都會被合併、序列化並與回應一起發送到客戶端，並且將在任何嵌套客戶端中介軟體的普通上下文物件上可用。在 `client` 中呼叫 `next` 的回傳物件包含從伺服器發送到客戶端的上下文，並且是型別安全的。中介軟體能夠從透過 `middleware` 函式鏈接的先前中介軟體推斷從伺服器發送到客戶端的上下文。

> [!WARNING] > `client` 中 `next` 的回傳型別只能從當前中介軟體鏈中已知的中介軟體推斷。因此，`next` 最準確的回傳型別是在中介軟體鏈末端的中介軟體中。

```tsx
const serverTimer = createMiddleware().server(async ({ next }) => {
  return next({
    sendContext: {
      // 將當前時間發送到客戶端
      timeFromServer: new Date(),
    },
  })
})

const requestLogger = createMiddleware()
  .middleware([serverTimer])
  .client(async ({ next }) => {
    const result = await next()
    // 哇！我們有來自伺服器的時間！
    console.log('Time from the server:', result.context.timeFromServer)

    return result
  })
```

## 讀取/修改伺服器回應 (Reading/Modifying the Server Response)

使用 `server` 方法的中介軟體在與伺服器函式相同的上下文中執行，因此你可以完全按照相同的[伺服器函式上下文工具](../server-functions#server-function-context)來讀取和修改請求標頭、狀態碼等。

## 修改客戶端請求 (Modifying the Client Request)

使用 `client` 方法的中介軟體在與伺服器函式**完全不同的客戶端上下文**中執行，因此你不能使用相同的工具來讀取和修改請求。但是，你仍然可以透過在呼叫 `next` 函式時回傳其他屬性來修改請求。目前支援的屬性有：

- `headers`：一個包含要添加到請求中的標頭的物件。

以下是一個使用此中介軟體為任何請求添加 `Authorization` 標頭的範例：

```tsx
import { getToken } from 'my-auth-library'

const authMiddleware = createMiddleware().client(async ({ next }) => {
  return next({
    headers: {
      Authorization: `Bearer ${getToken()}`,
    },
  })
})
```

## 使用中介軟體

中介軟體可以透過兩種不同的方式使用：

- **全域中介軟體 (Global Middleware)**：應為每個請求執行的中介軟體。
- **伺服器函式中介軟體 (Server Function Middleware)**：應為特定伺服器函式執行的中介軟體。

## 全域中介軟體 (Global Middleware)

全域中介軟體會自動為應用程式中的每個伺服器函式執行。這對於像身份驗證、日誌記錄和監控這樣的功能非常有用，這些功能應適用於所有請求。

要使用全域中介軟體，請在你的專案中建立一個 `global-middleware.ts` 檔案（通常位於 `app/global-middleware.ts`）。此檔案在客戶端和伺服器環境中運行，是你註冊全域中介軟體的地方。

以下是註冊全域中介軟體的方式：

```tsx
// app/global-middleware.ts
import { registerGlobalMiddleware } from '@tanstack/react-start'
import { authMiddleware } from './middleware'

registerGlobalMiddleware({
  middleware: [authMiddleware],
})
```

### 全域中介軟體型別安全性 (Global Middleware Type Safety)

全域中介軟體型別本質上與伺服器函式本身**分離**。這意味著，如果全域中介軟體向伺服器函式或其他伺服器函式特定中介軟體提供額外的上下文，型別不會自動傳遞給伺服器函式或其他伺服器函式特定中介軟體。

```tsx
// app/global-middleware.ts
registerGlobalMiddleware({
  middleware: [authMiddleware],
})
```

```tsx
// authMiddleware.ts
const authMiddleware = createMiddleware().server(({ next, context }) => {
  console.log(context.user) // <-- 這不會有型別！
  // ...
})
```

為了解決這個問題，將你嘗試參考的全域中介軟體添加到伺服器函式的中介軟體陣列中。**全域中介軟體將被去重為單一條目（全域實例），並且你的伺服器函式將接收正確的型別。**

以下是一個範例說明這如何運作：

```tsx
import { authMiddleware } from './authMiddleware'

const fn = createServerFn()
  .middleware([authMiddleware])
  .handler(async ({ context }) => {
    console.log(context.user)
    // ...
  })
```

## 中介軟體執行順序 (Middleware Execution Order)

中介軟體按依賴順序執行，從全域中介軟體開始，接著是伺服器函式中介軟體。以下範例將按以下順序記錄：

- `globalMiddleware1`
- `globalMiddleware2`
- `a`
- `b`
- `c`
- `d`

```tsx
const globalMiddleware1 = createMiddleware().server(async ({ next }) => {
  console.log('globalMiddleware1')
  return next()
})

const globalMiddleware2 = createMiddleware().server(async ({ next }) => {
  console.log('globalMiddleware2')
  return next()
})

registerGlobalMiddleware({
  middleware: [globalMiddleware1, globalMiddleware2],
})

const a = createMiddleware().server(async ({ next }) => {
  console.log('a')
  return next()
})

const b = createMiddleware()
  .middleware([a])
  .server(async ({ next }) => {
    console.log('b')
    return next()
  })

const c = createMiddleware()
  .middleware()
  .server(async ({ next }) => {
    console.log('c')
    return next()
  })

const d = createMiddleware()
  .middleware([b, c])
  .server(async () => {
    console.log('d')
  })

const fn = createServerFn()
  .middleware([d])
  .server(async () => {
    console.log('fn')
  })
```

## 環境樹搖 (Environment Tree Shaking)

中介軟體功能會根據每個產生的套件的環境進行樹搖。

- 在伺服器上，沒有任何內容會被樹搖，因此中介軟體中使用的所有程式碼都將包含在伺服器套件中。
- 在客戶端上，所有伺服器特定的程式碼都會從客戶端套件中移除。這意味著任何在 `server` 方法中使用的程式碼都會從客戶端套件中移除。如果 `validateClient` 設為 `true`，客戶端驗證程式碼將包含在客戶端套件中，否則 `data` 驗證程式碼也將被移除。
