---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:18:55.191Z'
title: 路徑參數
---

路徑參數 (Path Params) 用於匹配單一區段 (直到下一個 `/` 前的文字)，並將其值作為**命名**變數回傳。它們透過在路徑中使用 `$` 字元前綴來定義，後接要賦值的鍵值變數。以下是有效的路徑參數範例：

- `$postId`
- `$name`
- `$teamId`
- `about/$name`
- `team/$teamId`
- `blog/$postId`

由於路徑參數路由僅匹配到下一個 `/`，因此可以建立子路由來延續層級結構：

讓我們建立一個使用路徑參數匹配文章 ID 的文章路由檔案：

- `posts.$postId.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId)
  },
})
```

## 子路由可使用路徑參數

一旦路徑參數被解析，所有子路由都可以使用它。這表示如果我們為 `postRoute` 定義子路由，就能在子路由路徑中使用來自 URL 的 `postId` 變數！

## 在載入器 (Loaders) 中使用路徑參數

路徑參數會以 `params` 物件形式傳遞給載入器。此物件的鍵是路徑參數的名稱，值則是從實際 URL 路徑解析出的數值。例如，如果我們訪問 `/blog/123` URL，`params` 物件會是 `{ postId: '123' }`：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId)
  },
})
```

`params` 物件也會傳遞給 `beforeLoad` 選項：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  beforeLoad: async ({ params }) => {
    // 使用 params.postId 進行操作
  },
})
```

## 在元件中使用路徑參數

如果我們在 `postRoute` 中加入元件，可以透過路由的 `useParams` 鉤子 (hook) 從 URL 存取 `postId` 變數：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  component: PostComponent,
})

function PostComponent() {
  const { postId } = Route.useParams()
  return <div>Post {postId}</div>
}
```

> 🧠 小技巧：如果元件採用程式碼分割 (code-split)，可以使用 [getRouteApi 函式](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper) 來避免匯入 `Route` 設定，同時仍能存取型別化的 `useParams()` 鉤子。

## 在路由外部使用路徑參數

你也可以使用全域匯出的 `useParams` 鉤子，從應用程式的任何元件存取已解析的路徑參數。需傳遞 `strict: false` 選項給 `useParams`，表示要從不明確的位置存取參數：

```tsx
function PostComponent() {
  const { postId } = useParams({ strict: false })
  return <div>Post {postId}</div>
}
```

## 使用路徑參數導航

導航至含有路徑參數的路由時，TypeScript 會要求你以物件形式或函式形式傳遞參數。以下是物件形式的範例：

```tsx
function Component() {
  return (
    <Link to="/blog/$postId" params={{ postId: '123' }}>
      Post 123
    </Link>
  )
}
```

以下是函式形式的範例：

```tsx
function Component() {
  return (
    <Link to="/blog/$postId" params={(prev) => ({ ...prev, postId: '123' })}>
      Post 123
    </Link>
  )
}
```

注意，函式形式在需要保留 URL 中其他路由的現有參數時特別有用。這是因為函式形式會接收當前參數作為引數，讓你能視需要修改後回傳最終的參數物件。

## 允許的字元

預設情況下，路徑參數會透過 `encodeURIComponent` 進行跳脫。如果想允許其他有效的 URI 字元 (例如 `@` 或 `+`)，可以在 [RouterOptions](../api/router/RouterOptionsType.md#pathparamsallowedcharacters-property) 中指定。

使用範例：

```tsx
const router = createRouter({
  ...
  pathParamsAllowedCharacters: ['@']
})
```

以下是可接受的允許字元清單：
`;` `:` `@` `&` `=` `+` `$` `,`
