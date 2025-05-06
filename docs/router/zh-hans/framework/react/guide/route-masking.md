---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:33:56.000Z
title: 路由掩码
---

路由掩码是一种隐藏实际路由URL的方法，该URL会被持久化到浏览器的历史记录和地址栏中。这在您希望显示与实际导航路径不同的URL时非常有用，当分享该URL或（可选）页面重新加载时，会回退到显示的URL。以下是几个示例：

- 导航到类似 `/photo/5/modal` 的模态路由，但将实际URL掩码为 `/photos/5`
- 导航到类似 `/post/5/comments` 的模态路由，但将实际URL掩码为 `/posts/5`
- 导航到带有搜索参数 `?showLogin=true` 的路由，但将URL掩码为不包含该搜索参数
- 导航到带有搜索参数 `?modal=settings` 的路由，但将URL掩码为 `/settings`

这些场景都可以通过路由掩码实现，甚至可以扩展到支持更高级的模式，如[并行路由](./parallel-routes.md)。

## 路由掩码的工作原理

> [!IMPORTANT]
> 您**不需要**理解路由掩码的工作原理即可使用它。本节是为那些好奇其底层机制的人准备的。直接跳转到[如何使用路由掩码？](#how-do-i-use-route-masking)学习如何使用它！

路由掩码利用 `location.state` API 将期望的运行时位置存储在将被写入URL的位置中。它将这个运行时位置存储在 `__tempLocation` 状态属性下：

```tsx
const location = {
  pathname: '/photos/5',
  search: '',
  hash: '',
  state: {
    key: 'wesdfs',
    __tempKey: 'sadfasd',
    __tempLocation: {
      pathname: '/photo/5/modal',
      search: '',
      hash: '',
      state: {},
    },
  },
}
```

当路由器从历史记录中解析带有 `location.state.__tempLocation` 属性的位置时，它将使用该位置而不是从URL解析的位置。这允许您导航到类似 `/photos/5` 的路由，而实际上路由器会导航到 `/photo/5/modal`。发生这种情况时，历史位置会被保存回 `location.maskedLocation` 属性，以防我们需要知道**实际URL**。一个使用场景是在开发工具中，我们检测路由是否被掩码并显示实际URL而非掩码后的URL！

请记住，您无需担心这些细节。一切都在底层自动处理好了！

## 如何使用路由掩码？

路由掩码是一个简单的API，可以通过两种方式使用：

- 命令式地通过 `<Link>` 和 `navigate()` API 提供的 `mask` 选项
- 声明式地通过路由器的 `routeMasks` 选项

使用任一路由掩码API时，`mask` 选项接受与 `<Link>` 和 `navigate()` API 相同的导航对象。这意味着您可以使用熟悉的 `to`、`replace`、`state` 和 `search` 选项。唯一的区别是 `mask` 选项将用于掩码正在导航到的路由的URL。

> 🧠 `mask` 选项也是**类型安全**的！这意味着如果您使用TypeScript，尝试将无效的导航对象传递给 `mask` 选项时，您将获得类型错误。太棒了！

### 命令式路由掩码

`<Link>` 和 `navigate()` API 都接受 `mask` 选项，可用于掩码正在导航到的路由的URL。以下是使用 `<Link>` 组件的示例：

```tsx
<Link
  to="/photos/$photoId/modal"
  params={{ photoId: 5 }}
  mask={{
    to: '/photos/$photoId',
    params: {
      photoId: 5,
    },
  }}
>
  打开照片
</Link>
```

以下是使用 `navigate()` API 的示例：

```tsx
const navigate = useNavigate()

function onOpenPhoto() {
  navigate({
    to: '/photos/$photoId/modal',
    params: { photoId: 5 },
    mask: {
      to: '/photos/$photoId',
      params: {
        photoId: 5,
      },
    },
  })
}
```

### 声明式路由掩码

除了命令式API，您还可以使用路由器的 `routeMasks` 选项声明式地掩码路由。无需为每个 `<Link>` 或 `navigate()` 调用传递 `mask` 选项，您可以在路由器上创建一个路由掩码来掩码匹配特定模式的路由。以下是上述相同路由掩码的示例，但使用 `routeMasks` 选项：

// 以下示例使用以下代码

```tsx
import { createRouteMask } from '@tanstack/react-router'

const photoModalToPhotoMask = createRouteMask({
  routeTree,
  from: '/photos/$photoId/modal',
  to: '/photos/$photoId',
  params: (prev) => ({
    photoId: prev.photoId,
  }),
})

const router = createRouter({
  routeTree,
  routeMasks: [photoModalToPhotoMask],
})
```

创建路由掩码时，您需要传递至少包含以下内容的1个参数：

- `routeTree` - 路由掩码将应用到的路由树
- `from` - 路由掩码将应用到的路由ID
- `...navigateOptions` - `<Link>` 和 `navigate()` API 接受的标准 `to`、`search`、`params`、`replace` 等选项

> 🧠 `createRouteMask` 选项也是**类型安全**的！这意味着如果您使用TypeScript，尝试将无效的路由掩码传递给 `routeMasks` 选项时，您将获得类型错误。

## 分享URL时取消掩码

当URL从浏览器的本地历史堆栈中分离时，URL掩码数据将不再可用，因此URL会自动取消掩码。本质上，一旦您从历史记录中复制并粘贴URL，其掩码数据就会丢失...毕竟，这就是掩码URL的目的！

## 本地取消掩码默认设置

**默认情况下，当页面在本地重新加载时，URL不会取消掩码**。掩码数据存储在历史位置的 `location.state` 属性中，因此只要历史位置仍在历史堆栈的内存中，掩码数据就可用，URL将继续被掩码。

## 页面重新加载时取消掩码

**如上所述，默认情况下，页面重新加载时URL不会取消掩码**。

如果您希望在页面重新加载时本地取消掩码URL，您有3个选项，每个选项如果传递，将按优先级覆盖前一个：

- 将路由器的默认 `unmaskOnReload` 选项设置为 `true`
- 在使用 `createRouteMask()` 创建路由掩码时，从掩码函数返回 `unmaskOnReload: true` 选项
- 将 `unmaskOnReload: true` 选项传递给 `<Link>` 组件或 `navigate()` API
