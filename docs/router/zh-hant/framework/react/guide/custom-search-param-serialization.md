---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:20:45.390Z'
title: 自訂搜尋參數序列化
---

# 自訂搜尋參數序列化 (Custom Search Param Serialization)

預設情況下，TanStack Router 會使用 `JSON.stringify` 和 `JSON.parse` 自動解析和序列化你的 URL 搜尋參數 (Search Params)。這個過程除了對搜尋物件進行序列化和反序列化外，還包括對搜尋字串進行跳脫 (escaping) 和反跳脫 (unescaping)，這是處理 URL 搜尋參數的常見做法。

舉例來說，使用預設配置時，如果你有以下搜尋物件：

```tsx
const search = {
  page: 1,
  sort: 'asc',
  filters: { author: 'tanner', min_words: 800 },
}
```

它會被序列化並跳脫成以下搜尋字串：

```txt
?page=1&sort=asc&filters=%7B%22author%22%3A%22tanner%22%2C%22min_words%22%3A800%7D
```

我們可以用以下程式碼實現預設行為：

```tsx
import {
  createRouter,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'

const router = createRouter({
  // ...
  parseSearch: parseSearchWith(JSON.parse),
  stringifySearch: stringifySearchWith(JSON.stringify),
})
```

然而，這種預設行為可能不適用於所有使用情境。例如，你可能想使用不同的序列化格式，像是 base64 編碼，或者你可能想使用專門的序列化/反序列化函式庫，例如 [query-string](https://github.com/sindresorhus/query-string)、[JSURL2](https://github.com/wmertens/jsurl2) 或 [Zipson](https://jgranstrom.github.io/zipson/)。

這可以透過在 [`Router`](../api/router/RouterOptionsType.md#stringifysearch-method) 配置中提供自訂的序列化和反序列化函式給 `parseSearch` 和 `stringifySearch` 選項來實現。進行此操作時，你可以利用 TanStack Router 內建的輔助函式 `parseSearchWith` 和 `stringifySearchWith` 來簡化流程。

> [!TIP]
> 序列化和反序列化的一個重要面向是，你必須能夠在反序列化後得到相同的物件。這很重要，因為如果序列化和反序列化過程沒有正確執行，你可能會遺失一些資訊。例如，如果你使用的函式庫不支援巢狀物件，你可能會在反序列化搜尋字串時遺失巢狀物件。

![展示 URL 搜尋參數序列化和反序列化的冪等性質的圖表](https://raw.githubusercontent.com/TanStack/router/main/docs/router/assets/search-serialization-deserialization-idempotency.jpg)

以下是一些如何在 TanStack Router 中自訂搜尋參數序列化的範例：

## 使用 Base64

常見的做法是對搜尋參數進行 base64 編碼，以實現跨瀏覽器和 URL 解碼工具等的最大相容性。這可以用以下程式碼實現：

```tsx
import {
  Router,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'

const router = createRouter({
  parseSearch: parseSearchWith((value) => JSON.parse(decodeFromBinary(value))),
  stringifySearch: stringifySearchWith((value) =>
    encodeToBinary(JSON.stringify(value)),
  ),
})

function decodeFromBinary(str: string): string {
  return decodeURIComponent(
    Array.prototype.map
      .call(atob(str), function (c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      })
      .join(''),
  )
}

function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

> [⚠️ 為什麼這個程式碼片段不使用 atob/btoa？](#safe-binary-encodingdecoding)

因此，如果我們使用這個配置將先前的物件轉換為搜尋字串，它會看起來像這樣：

```txt
?page=1&sort=asc&filters=eyJhdXRob3IiOiJ0YW5uZXIiLCJtaW5fd29yZHMiOjgwMH0%3D
```

> [!WARNING]
> 如果你將使用者輸入序列化為 Base64，可能會導致與 URL 反序列化發生衝突。這可能導致意外行為，例如 URL 無法正確解析或被解釋為不同的值。為避免這種情況，你應該使用安全的二進位編碼/解碼方法來編碼搜尋參數（見下文）。

## 使用 query-string 函式庫

[query-string](https://github.com/sindresorhus/query-string) 函式庫因為能可靠地解析和字串化查詢字串而廣受歡迎。你可以使用它來自訂搜尋參數的序列化格式。這可以用以下程式碼實現：

```tsx
import { createRouter } from '@tanstack/react-router'
import qs from 'query-string'

const router = createRouter({
  // ...
  stringifySearch: stringifySearchWith((value) =>
    qs.stringify(value, {
      // ...選項
    }),
  ),
  parseSearch: parseSearchWith((value) =>
    qs.parse(value, {
      // ...選項
    }),
  ),
})
```

因此，如果我們使用這個配置將先前的物件轉換為搜尋字串，它會看起來像這樣：

```txt
?page=1&sort=asc&filters=author%3Dtanner%26min_words%3D800
```

## 使用 JSURL2 函式庫

[JSURL2](https://github.com/wmertens/jsurl2) 是一個非標準函式庫，可以在保持可讀性的同時壓縮 URL。這可以用以下程式碼實現：

```tsx
import {
  Router,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'
import { parse, stringify } from 'jsurl2'

const router = createRouter({
  // ...
  parseSearch: parseSearchWith(parse),
  stringifySearch: stringifySearchWith(stringify),
})
```

因此，如果我們使用這個配置將先前的物件轉換為搜尋字串，它會看起來像這樣：

```txt
?page=1&sort=asc&filters=(author~tanner~min*_words~800)~
```

## 使用 Zipson 函式庫

[Zipson](https://jgranstrom.github.io/zipson/) 是一個非常使用者友好且高效的 JSON 壓縮函式庫（無論是在執行時效能還是壓縮效能方面）。要使用它來壓縮你的搜尋參數（這也需要對它們進行跳脫/反跳脫和 base64 編碼/解碼），你可以使用以下程式碼：

```tsx
import {
  Router,
  parseSearchWith,
  stringifySearchWith,
} from '@tanstack/react-router'
import { stringify, parse } from 'zipson'

const router = createRouter({
  parseSearch: parseSearchWith((value) => parse(decodeFromBinary(value))),
  stringifySearch: stringifySearchWith((value) =>
    encodeToBinary(stringify(value)),
  ),
})

function decodeFromBinary(str: string): string {
  return decodeURIComponent(
    Array.prototype.map
      .call(atob(str), function (c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      })
      .join(''),
  )
}

function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

> [⚠️ 為什麼這個程式碼片段不使用 atob/btoa？](#safe-binary-encodingdecoding)

因此，如果我們使用這個配置將先前的物件轉換為搜尋字串，它會看起來像這樣：

```txt
?page=1&sort=asc&filters=JTdCJUMyJUE4YXV0aG9yJUMyJUE4JUMyJUE4dGFubmVyJUMyJUE4JUMyJUE4bWluX3dvcmRzJUMyJUE4JUMyJUEyQ3UlN0Q%3D
```

<hr>

### 安全的二進位編碼/解碼 (Safe Binary Encoding/Decoding)

在瀏覽器中，`atob` 和 `btoa` 函式不保證能正確處理非 UTF8 字元。我們建議改用這些編碼/解碼工具：

要將字串編碼為二進位字串：

```typescript
export function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

要將二進位字串解碼為字串：

```typescript
export function decodeFromBinary(str: string): string {
  return decodeURIComponent(
    Array.prototype.map
      .call(atob(str), function (c) {
        return '%' + ('00' + c.charCodeAt(0).toString(16)).slice(-2)
      })
      .join(''),
  )
}
```
