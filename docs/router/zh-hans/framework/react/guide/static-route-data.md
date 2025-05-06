---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:37:01.000Z
title: 静态路由数据
---

在创建路由时，您可以选择在路由选项中指定一个 `staticData` 属性。这个对象可以包含您需要的任何内容，只要在创建路由时能够同步获取即可。

除了可以从路由本身访问这些数据外，您还可以通过任意匹配项的 `match.staticData` 属性来访问这些数据。

## 示例

- `posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  staticData: {
    customData: 'Hello!',
  },
})
```

之后，您可以在任何能够访问路由的地方获取这些数据，包括可以映射回其原始路由的匹配项。

- `__root.tsx`

```tsx
import { createRootRoute } from '@tanstack/react-router'

export const Route = createRootRoute({
  component: () => {
    const matches = useMatches()

    return (
      <div>
        {matches.map((match) => {
          return <div key={match.id}>{match.staticData.customData}</div>
        })}
      </div>
    )
  },
})
```

## 强制静态数据

如果您想强制要求某个路由必须包含静态数据，可以通过声明合并的方式为路由的静态选项添加类型：

```tsx
declare module '@tanstack/react-router' {
  interface StaticDataRouteOption {
    customData: string
  }
}
```

现在，如果您尝试创建一个不包含 `customData` 属性的路由，将会收到类型错误提示：

```tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  staticData: {
    // 类型“{ customData: number; }”中缺少属性“customData”，但类型“StaticDataRouteOption”中需要该属性。ts(2741)
  },
})
```

## 可选静态数据

如果您想让静态数据变为可选项，只需在属性后添加 `?`：

```tsx
declare module '@tanstack/react-router' {
  interface StaticDataRouteOption {
    customData?: string
  }
}
```

只要 `StaticDataRouteOption` 中存在任何必填属性，您就必须传入一个对象。
