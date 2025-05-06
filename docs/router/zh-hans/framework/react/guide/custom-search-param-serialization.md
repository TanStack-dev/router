---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:33:56.000Z
title: 自定义搜索参数序列化
---

以下是翻译后的中文文档，保持所有代码块、Markdown格式、HTML标签和变量不变：

---

默认情况下，TanStack Router 使用 `JSON.stringify` 和 `JSON.parse` 自动解析和序列化 URL 搜索参数。这一过程除了对搜索对象进行序列化和反序列化外，还包括对搜索字符串进行转义和反转义，这是处理 URL 搜索参数的常见做法。

例如，在默认配置下，如果你有以下搜索对象：

```tsx
const search = {
  page: 1,
  sort: 'asc',
  filters: { author: 'tanner', min_words: 800 },
}
```

它会被序列化并转义为以下搜索字符串：

```txt
?page=1&sort=asc&filters=%7B%22author%22%3A%22tanner%22%2C%22min_words%22%3A800%7D
```

我们可以通过以下代码实现默认行为：

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

然而，这种默认行为可能并不适用于所有场景。例如，你可能希望使用其他序列化格式（如 base64 编码），或使用专门构建的序列化/反序列化库，比如 [query-string](https://github.com/sindresorhus/query-string)、[JSURL2](https://github.com/wmertens/jsurl2) 或 [Zipson](https://jgranstrom.github.io/zipson/)。

你可以通过在 [`Router`](../api/router/RouterOptionsType.md#stringifysearch-method) 配置中为 `parseSearch` 和 `stringifySearch` 选项提供自定义的序列化和反序列化函数来实现这一点。在此过程中，你可以利用 TanStack Router 内置的辅助函数 `parseSearchWith` 和 `stringifySearchWith` 来简化流程。

> [!TIP]
> 序列化和反序列化的一个重要特性是能够在反序列化后得到相同的对象。这一点非常重要，因为如果序列化和反序列化过程不正确，可能会导致信息丢失。例如，如果你使用的库不支持嵌套对象，那么在反序列化搜索字符串时可能会丢失嵌套对象。

![展示 URL 搜索参数序列化和反序列化的幂等性示意图](https://raw.githubusercontent.com/TanStack/router/main/docs/router/assets/search-serialization-deserialization-idempotency.jpg)

以下是一些自定义 TanStack Router 中搜索参数序列化的示例：

## 使用 Base64

通常，为了在浏览器和 URL 解析工具等场景中实现最大兼容性，可以对搜索参数进行 base64 编码。可以通过以下代码实现：

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

> [⚠️ 为什么此代码片段不使用 atob/btoa？](#safe-binary-encodingdecoding)

如果使用此配置将之前的对象转换为搜索字符串，结果如下：

```txt
?page=1&sort=asc&filters=eyJhdXRob3IiOiJ0YW5uZXIiLCJtaW5fd29yZHMiOjgwMH0%3D
```

> [!WARNING]
> 如果将用户输入序列化为 Base64，可能会导致与 URL 反序列化冲突。这可能会引发意外行为，例如 URL 无法正确解析或被解释为其他值。为避免此问题，应使用安全的二进制编码/解码方法对搜索参数进行编码（见下文）。

## 使用 query-string 库

[query-string](https://github.com/sindresorhus/query-string) 是一个流行的库，能够可靠地解析和序列化查询字符串。你可以使用它来自定义搜索参数的序列化格式。代码如下：

```tsx
import { createRouter } from '@tanstack/react-router'
import qs from 'query-string'

const router = createRouter({
  // ...
  stringifySearch: stringifySearchWith((value) =>
    qs.stringify(value, {
      // ...options
    }),
  ),
  parseSearch: parseSearchWith((value) =>
    qs.parse(value, {
      // ...options
    }),
  ),
})
```

如果使用此配置将之前的对象转换为搜索字符串，结果如下：

```txt
?page=1&sort=asc&filters=author%3Dtanner%26min_words%3D800
```

## 使用 JSURL2 库

[JSURL2](https://github.com/wmertens/jsurl2) 是一个非标准库，可以在保持可读性的同时压缩 URL。代码如下：

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

如果使用此配置将之前的对象转换为搜索字符串，结果如下：

```txt
?page=1&sort=asc&filters=(author~tanner~min*_words~800)~
```

## 使用 Zipson 库

[Zipson](https://jgranstrom.github.io/zipson/) 是一个用户友好且高性能的 JSON 压缩库（在运行时性能和压缩结果上均表现优异）。要使用它压缩搜索参数（同时需要对其进行转义/反转义和 base64 编码/解码），代码如下：

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

> [⚠️ 为什么此代码片段不使用 atob/btoa？](#safe-binary-encodingdecoding)

如果使用此配置将之前的对象转换为搜索字符串，结果如下：

```txt
?page=1&sort=asc&filters=JTdCJUMyJUE4YXV0aG9yJUMyJUE4JUMyJUE4dGFubmVyJUMyJUE4JUMyJUE4bWluX3dvcmRzJUMyJUE4JUMyJUEyQ3UlN0Q%3D
```

<hr>

### 安全的二进制编码/解码

在浏览器中，`atob` 和 `btoa` 函数不能保证正确处理非 UTF8 字符。我们建议改用以下编码/解码工具：

将字符串编码为二进制字符串：

```typescript
export function encodeToBinary(str: string): string {
  return btoa(
    encodeURIComponent(str).replace(/%([0-9A-F]{2})/g, function (match, p1) {
      return String.fromCharCode(parseInt(p1, 16))
    }),
  )
}
```

将二进制字符串解码为字符串：

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
