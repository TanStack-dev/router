---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:26:00.000Z
title: 路径参数
---

路径参数用于匹配单个路径段（直到下一个`/`之前的文本），并将其值作为**命名**变量返回给你。它们通过在路径中使用`$`字符前缀定义，后跟要赋值的键变量。以下是有效的路径参数路径示例：

- `$postId`
- `$name`
- `$teamId`
- `about/$name`
- `team/$teamId`
- `blog/$postId`

由于路径参数路由仅匹配到下一个`/`，可以创建子路由来继续表达层级结构：

让我们创建一个使用路径参数匹配文章ID的路由文件：

- `posts.$postId.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId)
  },
})
```

## 子路由可以使用路径参数

一旦路径参数被解析，它对所有子路由都可用。这意味着如果我们在`postRoute`中定义一个子路由，可以在子路由路径中使用URL中的`postId`变量！

## 加载器中的路径参数

路径参数作为`params`对象传递给加载器。该对象的键是路径参数的名称，值是从实际URL路径中解析出的值。例如，如果我们访问`/blog/123` URL，`params`对象将是`{ postId: '123' }`：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    return fetchPost(params.postId)
  },
})
```

`params`对象也会传递给`beforeLoad`选项：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  beforeLoad: async ({ params }) => {
    // 对params.postId进行操作
  },
})
```

## 组件中的路径参数

如果我们在`postRoute`中添加一个组件，可以通过路由的`useParams`钩子访问URL中的`postId`变量：

```tsx
export const Route = createFileRoute('/posts/$postId')({
  component: PostComponent,
})

function PostComponent() {
  const { postId } = Route.useParams()
  return <div>Post {postId}</div>
}
```

> 🧠 小技巧：如果你的组件是代码分割的，可以使用[getRouteApi函数](./code-splitting.md#manually-accessing-route-apis-in-other-files-with-the-getrouteapi-helper)来避免导入`Route`配置以访问类型化的`useParams()`钩子。

## 路由外的路径参数

你也可以使用全局导出的`useParams`钩子从应用程序的任何组件访问已解析的路径参数。需要向`useParams`传递`strict: false`选项，表示你想从模糊位置访问参数：

```tsx
function PostComponent() {
  const { postId } = useParams({ strict: false })
  return <div>Post {postId}</div>
}
```

## 使用路径参数导航

当导航到带有路径参数的路由时，TypeScript会要求你将参数作为对象或返回参数对象的函数传递。

以下是对象风格的示例：

```tsx
function Component() {
  return (
    <Link to="/blog/$postId" params={{ postId: '123' }}>
      Post 123
    </Link>
  )
}
```

以下是函数风格的示例：

```tsx
function Component() {
  return (
    <Link to="/blog/$postId" params={(prev) => ({ ...prev, postId: '123' })}>
      Post 123
    </Link>
  )
}
```

注意，函数风格在你需要保留URL中其他路由的参数时非常有用。这是因为函数风格会接收当前参数作为参数，允许你根据需要修改它们并返回最终的参数对象。

## 允许的字符

默认情况下，路径参数会使用`encodeURIComponent`进行转义。如果你想允许其他有效的URI字符（如`@`或`+`），可以在[RouterOptions](../api/router/RouterOptionsType.md#pathparamsallowedcharacters-property)中指定。

示例用法：

```tsx
const router = createRouter({
  ...
  pathParamsAllowedCharacters: ['@']
})
```

以下是可接受的允许字符列表：
`;` `:` `@` `&` `=` `+` `$` `,`
