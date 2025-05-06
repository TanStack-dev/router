---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:27:12.000Z
title: 数据加载
id: data-loading
---

# 数据加载

数据加载是Web应用程序的常见关注点，与路由密切相关。在加载应用程序页面时，理想情况是尽早并行获取并满足页面的所有异步需求。路由是协调这些异步依赖项的最佳位置，因为它通常是应用程序中唯一在内容渲染前就知道用户去向的地方。

您可能熟悉Next.js的`getServerSideProps`或Remix/React-Router的`loader`。TanStack Router具有类似的功能，可以并行预加载/按路由加载资源，通过Suspense获取数据实现尽可能快的渲染。

除了路由器的这些常规功能外，TanStack Router更进一步，提供了**内置的SWR缓存**，这是一个用于路由加载器的长期内存缓存层。这意味着您可以使用TanStack Router预加载路由数据以实现即时加载，或临时缓存先前访问过的路由数据以供后续使用。

## 路由加载生命周期

每次检测到URL/历史记录更新时，路由器会执行以下序列：

- 路由匹配（自上而下）
  - `route.params.parse`
  - `route.validateSearch`
- 路由预加载（串行）
  - `route.beforeLoad`
  - `route.onError`
    - `route.errorComponent` / `parentRoute.errorComponent` / `router.defaultErrorComponent`
- 路由加载（并行）
  - `route.component.preload?`
  - `route.loader`
    - `route.pendingComponent`（可选）
    - `route.component`
  - `route.onError`
    - `route.errorComponent` / `parentRoute.errorComponent` / `router.defaultErrorComponent`

## 使用路由缓存还是不使用？

TanStack的路由缓存很可能非常适合大多数中小型应用程序，但重要的是要理解使用它与更健壮的缓存解决方案（如TanStack Query）之间的权衡：

TanStack路由缓存的优点：

- 内置、易于使用，无需额外依赖
- 处理去重、预加载、加载、过时重验证（stale-while-revalidate）、按路由后台重新获取
- 粗粒度失效（一次性失效所有路由和缓存）
- 自动垃圾回收
- 非常适合路由间共享数据较少的应用程序
- 对SSR“开箱即用”

TanStack路由缓存的缺点：

- 没有持久化适配器/模型
- 路由间没有共享缓存/去重
- 没有内置的变更API（许多示例中提供了基本的`useMutation`钩子，可能足以满足许多用例）
- 没有内置的缓存级乐观更新API（您仍然可以使用类似`useMutation`钩子的临时状态在组件级别实现这一点）

> [!TIP]
> 如果您立即知道想要或需要使用更健壮的解决方案（如TanStack Query），请跳转到[外部数据加载](./external-data-loading.md)指南。

## 使用路由缓存

路由缓存是内置的，只需从任何路由的`loader`函数返回数据即可。让我们学习如何使用！

## 路由`loader`

路由`loader`函数在加载路由匹配时调用。它们接收一个参数，该参数是一个包含许多有用属性的对象。我们稍后会详细介绍这些属性，但首先让我们看一个路由`loader`函数的示例：

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
})
```

## `loader`参数

`loader`函数接收一个包含以下属性的对象：

- `abortController` - 路由的abortController。当路由卸载或Route不再相关且当前`loader`函数调用过时时，其信号会被取消。
- `cause` - 当前路由匹配的原因，`enter`或`stay`。
- `context` - 路由的上下文对象，是以下内容的合并：
  - 父路由上下文
  - 由`beforeLoad`选项提供的此路由上下文
- `deps` - 从`Route.loaderDeps`函数返回的对象值。如果未定义`Route.loaderDeps`，则提供空对象。
- `location` - 当前位置
- `params` - 路由的路径参数
- `parentMatchPromise` - `Promise<RouteMatch>`（根路由为`undefined`）
- `preload` - 布尔值，当路由被预加载而非加载时为`true`
- `route` - 路由本身

使用这些参数，我们可以做很多很酷的事情，但首先让我们看看如何控制它以及何时调用`loader`函数。

## 从`loader`消费数据

要从`loader`消费数据，请使用Route对象上定义的`useLoaderData`钩子。

```tsx
const posts = Route.useLoaderData()
```

如果您无法直接访问路由对象（即您位于当前路由的组件树深处），可以使用`getRouteApi`访问相同的钩子（以及Route对象上的其他钩子）。这应优先于导入Route对象，后者可能会导致循环依赖。

```tsx
import { getRouteApi } from '@tanstack/react-router'

// 在您的组件中

const routeApi = getRouteApi('/posts')
const data = routeApi.useLoaderData()
```

## 基于依赖项的过时重验证缓存

TanStack Router为路由加载器提供了一个内置的过时重验证（Stale-While-Revalidate）缓存层，其键基于路由的依赖项：

- 路由的完全解析路径名
  - 例如`/posts/1`与`/posts/2`
- 由`loaderDeps`选项提供的任何其他依赖项
  - 例如`loaderDeps: ({ search: { pageIndex, pageSize } }) => ({ pageIndex, pageSize })`

使用这些依赖项作为键，TanStack Router将缓存从路由`loader`函数返回的数据，并用它来满足对相同路由匹配的后续请求。这意味着如果路由数据已在缓存中，它将立即返回，然后**可能**在后台重新获取，具体取决于数据的“新鲜度”。

### 关键选项

为了控制路由依赖项和“新鲜度”，TanStack Router提供了大量选项来控制路由加载器的键和缓存行为。让我们按您最可能使用的顺序来看一下：

- `routeOptions.loaderDeps`
  - 一个函数，提供路由器的搜索参数并返回一个依赖项对象供`loader`函数使用。当这些依赖项在导航间发生变化时，无论`staleTime`如何，都会导致路由重新加载。依赖项使用深度相等检查进行比较。
- `routeOptions.staleTime`
- `routerOptions.defaultStaleTime`
  - 尝试加载时路由数据应被视为新鲜的毫秒数。
- `routeOptions.preloadStaleTime`
- `routerOptions.defaultPreloadStaleTime`
  - 尝试预加载时路由数据应被视为新鲜的毫秒数。
- `routeOptions.gcTime`
- `routerOptions.defaultGcTime`
  - 路由数据在被垃圾回收前应保留在缓存中的毫秒数。
- `routeOptions.shouldReload`
  - 一个函数，接收与`beforeLoad`和`loaderContext`相同的参数，并返回一个布尔值，指示路由是否应重新加载。这提供了在`staleTime`和`loaderDeps`之外控制路由何时重新加载的额外级别，可用于实现类似于Remix的`shouldLoad`选项的模式。

### ⚠️ 一些重要默认值

- 默认情况下，`staleTime`设置为`0`，意味着路由数据将始终被视为过时，并在路由重新匹配时始终在后台重新加载。
- 默认情况下，先前预加载的路由被视为新鲜**30秒**。这意味着如果路由被预加载，然后在30秒内再次预加载，第二次预加载将被忽略。这防止不必要的预加载过于频繁发生。**当路由正常加载时，使用标准的`staleTime`。**
- 默认情况下，`gcTime`设置为**30分钟**，意味着任何30分钟内未被访问的路由数据将被垃圾回收并从缓存中移除。
- `router.invalidate()`将强制所有活动路由立即重新加载其加载器，并将每个缓存的路由数据标记为过时。

### 使用`loaderDeps`访问搜索参数

假设`/posts`路由通过搜索参数`offset`和`limit`支持分页。为了使缓存能唯一存储这些数据，我们需要通过`loaderDeps`函数访问这些搜索参数。通过明确标识它们，`/posts`的不同`offset`和`limit`的路由匹配不会混淆！

一旦我们有了这些依赖项，当依赖项更改时，路由将始终重新加载。

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loaderDeps: ({ search: { offset, limit } }) => ({ offset, limit }),
  loader: ({ deps: { offset, limit } }) =>
    fetchPosts({
      offset,
      limit,
    }),
})
```

### 使用`staleTime`控制数据被视为新鲜的时间

默认情况下，导航的`staleTime`设置为`0`毫秒（预加载为30秒），这意味着路由数据将始终被视为过时，并在路由匹配和导航到时始终在后台重新加载。

**这对于大多数用例来说是一个很好的默认值，但您可能会发现某些路由数据更静态或加载成本更高。**在这些情况下，您可以使用`staleTime`选项来控制路由数据在导航中被视为新鲜的时间。让我们看一个示例：

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  // 路由数据被视为新鲜10秒
  staleTime: 10_000,
})
```

通过将`10_000`传递给`staleTime`选项，我们告诉路由器将路由数据视为新鲜10秒。这意味着如果用户在最后一次加载器结果的10秒内从`/about`导航到`/posts`，路由数据将不会重新加载。如果用户在10秒后从`/about`导航到`/posts`，路由数据将在**后台**重新加载。

## 关闭过时重验证缓存

要禁用路由的过时重验证缓存，将`staleTime`选项设置为`Infinity`：

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  staleTime: Infinity,
})
```

您甚至可以通过在路由器上设置`defaultStaleTime`选项为所有路由关闭此功能：

```tsx
const router = createRouter({
  routeTree,
  defaultStaleTime: Infinity,
})
```

## 使用`shouldReload`和`gcTime`选择退出缓存

类似于Remix的默认功能，您可能希望配置路由仅在进入或关键加载器依赖项更改时加载。您可以通过结合使用`gcTime`选项和`shouldReload`选项来实现这一点，`shouldReload`接受一个`boolean`或一个函数，该函数接收与`beforeLoad`和`loaderContext`相同的参数，并返回一个布尔值，指示路由是否应重新加载。

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loaderDeps: ({ search: { offset, limit } }) => ({ offset, limit }),
  loader: ({ deps }) => fetchPosts(deps),
  // 路由卸载后不缓存此路由数据
  gcTime: 0,
  // 仅在用户导航到路由或依赖项更改时重新加载路由
  shouldReload: false,
})
```

### 选择退出缓存同时仍预加载

即使您选择退出路由数据的短期缓存，您仍然可以获得预加载的好处！使用上述配置，预加载仍将“正常工作”，使用默认的`preloadGcTime`。这意味着如果路由被预加载，然后导航到，路由数据将被视为新鲜且不会重新加载。

要选择退出预加载，不要通过`routerOptions.defaultPreload`或`routeOptions.preload`选项启用它。

## 将所有加载器事件传递给外部缓存

我们在[外部数据加载](./external-data-loading.md)页面中分解了这个用例，但如果您想使用像TanStack Query这样的外部缓存，可以通过将所有加载器事件传递给外部缓存来实现。只要您使用默认值，唯一需要做的更改是将路由器上的`defaultPreloadStaleTime`选项设置为`0`：

```tsx
const router = createRouter({
  routeTree,
  defaultPreloadStaleTime: 0,
})
```

这将确保每次预加载、加载和重新加载事件都会触发您的`loader`函数，然后可以由您的外部缓存处理和去重。

## 使用路由器上下文

传递给`loader`函数的`context`参数是一个包含以下内容合并的对象：

- 父路由上下文
- 由`beforeLoad`选项提供的此路由上下文

从路由器的顶部开始，您可以通过`context`选项向路由器传递初始上下文。此上下文将对路由器中的所有路由可用，并在匹配时被每个路由复制和扩展。这是通过`beforeLoad`选项向路由传递上下文实现的。此上下文将对路由的所有子路由可用。生成的上下文将对路由的`loader`函数可用。

在此示例中，我们将在路由上下文中创建一个函数来获取帖子，然后在`loader`函数中使用它。

> 🧠 上下文是依赖注入的强大工具。您可以使用它向路由器和路由注入服务、钩子和其他对象。您还可以使用路由的`beforeLoad`选项在路由树中逐层传递数据。

- `/utils/fetchPosts.tsx`

```tsx
export const fetchPosts = async () => {
  const res = await fetch(`/api/posts?page=${pageIndex}`)
  if (!res.ok) throw new Error('Failed to fetch posts')
  return res.json()
}
```

- `/routes/__root.tsx`

```tsx
import { createRootRouteWithContext } from '@tanstack/react-router'

// 使用createRootRouteWithContext<{...}>()函数创建根路由，并传递您希望在路由器上下文中可用的任何类型。
export const Route = createRootRouteWithContext<{
  fetchPosts: typeof fetchPosts
}>()() // 注意：双调用是有意的，因为createRootRouteWithContext是一个工厂函数;)
```

- `/routes/posts.tsx`

```tsx
import { createFileRoute } from '@tanstack/react-router'

// 注意我们的postsRoute如何引用上下文以获取fetchPosts函数
// 这可以成为在路由器和路由之间进行依赖注入的强大工具。
export const Route = createFileRoute('/posts')({
  loader: ({ context: { fetchPosts } }) => fetchPosts(),
})
```

- `/router.tsx`

````tsx
import { routeTree } from './routeTree.gen'
```以下是翻译后的中文文档，保持所有代码块、Markdown格式、HTML标签和变量不变：

// 使用你的routerContext创建一个新路由
// 这将要求你满足routerContext的类型要求
const router = createRouter({
  routeTree,
  context: {
    // 将fetchPosts函数提供给路由上下文
    fetchPosts,
  },
})
````

## 使用路径参数

要在`loader`函数中使用路径参数，可通过函数参数的`params`属性访问它们。以下是一个示例：

```tsx
// routes/posts.$postId.tsx
export const Route = createFileRoute('/posts/$postId')({
  loader: ({ params: { postId } }) => fetchPostById(postId),
})
```

## 使用路由上下文

向路由传递全局上下文固然很好，但如果想提供特定于路由的上下文呢？这时`beforeLoad`选项就派上用场了。`beforeLoad`是一个在尝试加载路由前运行的函数，接收与`loader`相同的参数。除了能重定向潜在匹配、阻止加载请求等功能外，它还可以返回一个对象，该对象会被合并到路由的上下文中。以下是一个通过`beforeLoad`选项向路由上下文注入数据的例子：

```tsx
// /routes/posts.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  // 将fetchPosts函数传递给路由上下文
  beforeLoad: () => ({
    fetchPosts: () => console.info('foo'),
  }),
  loader: ({ context: { fetchPosts } }) => {
    console.info(fetchPosts()) // 'foo'

    // ...
  },
})
```

## 在加载器中使用搜索参数

> ❓ 但是等等Tanner...我的搜索参数跑哪去了？！

你可能在疑惑为什么`search`没有直接出现在`loader`函数的参数中。我们这样设计是有意为之，目的是帮助你更好地使用。来看看原因：

- 在加载器函数中使用搜索参数，通常意味着这些搜索参数也应被用来唯一标识正在加载的数据。例如，你可能有一个路由使用`pageIndex`这样的搜索参数来唯一标识路由匹配中的数据。或者想象一个`/users/user`路由，它使用`userId`搜索参数来标识应用中的特定用户，你可能会这样设计URL：`/users/user?userId=123`。这意味着你的`user`路由需要额外帮助来识别特定用户。
- 直接在加载器函数中访问搜索参数可能导致缓存和预加载中的错误，因为加载的数据不与当前URL路径和搜索参数唯一对应。例如，你可能要求`/posts`路由预加载第2页的结果，但如果路由配置中没有区分页面，你最终会在`/posts`或`?page=1`屏幕上获取、存储和显示第2页的数据，而不是在后台预加载！
- 在搜索参数和加载器函数之间设置一个门槛，能让路由理解你的依赖和响应性。

```tsx
// /routes/users.user.tsx
export const Route = createFileRoute('/users/user')({
  validateSearch: (search) =>
    search as {
      userId: string
    },
  loaderDeps: ({ search: { userId } }) => ({
    userId,
  }),
  loader: async ({ deps: { userId } }) => getUser(userId),
})
```

### 通过`routeOptions.loaderDeps`访问搜索参数

```tsx
// /routes/posts.tsx
export const Route = createFileRoute('/posts')({
  // 使用zod验证和解析搜索参数
  validateSearch: z.object({
    offset: z.number().int().nonnegative().catch(0),
  }),
  // 通过loaderDeps函数将offset传递给加载器依赖
  loaderDeps: ({ search: { offset } }) => ({ offset }),
  // 在加载器函数中使用上下文中的offset
  loader: async ({ deps: { offset } }) =>
    fetchPosts({
      offset,
    }),
})
```

## 使用中止信号

`loader`函数的`abortController`属性是一个[AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)。当路由卸载或`loader`调用过时时，其信号会被取消。这在路由卸载或路由参数变化时取消网络请求非常有用。以下是一个与fetch调用一起使用的示例：

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: ({ abortController }) =>
    fetchPosts({
      // 将此传递给底层fetch调用或任何支持signal的对象
      signal: abortController.signal,
    }),
})
```

## 使用`preload`标志

`loader`函数的`preload`属性是一个布尔值，当路由被预加载而非加载时为`true`。一些数据加载库可能以不同于标准fetch的方式处理预加载，因此你可能想将`preload`传递给数据加载库，或使用它来执行适当的数据加载逻辑：

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: async ({ preload }) =>
    fetchPosts({
      maxAge: preload ? 10_000 : 0, // 预加载的数据应保留更久
    }),
})
```

## 处理慢加载器

理想情况下，大多数路由加载器都能在短时间内解析其数据，无需渲染占位符spinner，只需依赖suspense在路由完全准备好时渲染下一个路由。但当渲染路由组件所需的关键数据较慢时，你有两个选择：

- 将快速和慢速数据拆分为单独的promise，并在快速数据加载后`defer`慢速数据（参见[延迟数据加载](./deferred-data-loading.md)指南）。
- 在乐观suspense阈值后显示一个待处理组件，直到所有数据准备就绪（见下文）。

## 显示待处理组件

**默认情况下，TanStack Router会在加载器解析时间超过1秒时显示待处理组件。** 这是一个乐观阈值，可通过以下方式配置：

- `routeOptions.pendingMs` 或
- `routerOptions.defaultPendingMs`

当超过待处理时间阈值时，如果配置了`pendingComponent`选项，路由将渲染该组件。

## 避免待处理组件闪烁

如果使用待处理组件，最不希望看到的是待处理时间阈值刚满足，数据就立即解析，导致待处理组件出现刺眼的闪烁。为避免这种情况，**TanStack Router默认会显示待处理组件至少500ms**。这是一个乐观阈值，可通过以下方式配置：

- `routeOptions.pendingMinMs` 或
- `routerOptions.defaultPendingMinMs`

## 处理错误

TanStack Router提供了几种处理路由加载生命周期中发生的错误的方式。来看看它们。

### 使用`routeOptions.onError`处理错误

`routeOptions.onError`选项是一个函数，当路由加载过程中发生错误时被调用。

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  onError: ({ error }) => {
    // 记录错误
    console.error(error)
  },
})
```

### 使用`routeOptions.onCatch`处理错误

`routeOptions.onCatch`选项是一个函数，当路由的CatchBoundary捕获到错误时被调用。

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  onCatch: ({ error, errorInfo }) => {
    // 记录错误
    console.error(error)
  },
})
```

### 使用`routeOptions.errorComponent`处理错误

`routeOptions.errorComponent`选项是一个组件，当路由加载或渲染生命周期中发生错误时渲染。它接收以下props：

- `error` - 发生的错误
- `reset` - 重置内部`CatchBoundary`的函数

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error }) => {
    // 渲染错误信息
    return <div>{error.message}</div>
  },
})
```

`reset`函数可用于允许用户重试渲染错误边界的正常子元素：

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error, reset }) => {
    return (
      <div>
        {error.message}
        <button
          onClick={() => {
            // 重置路由错误边界
            reset()
          }}
        >
          重试
        </button>
      </div>
    )
  },
})
```

如果错误是路由加载的结果，你应该调用`router.invalidate()`，这将协调路由重新加载和错误边界重置：

```tsx
// routes/posts.tsx
export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error, reset }) => {
    const router = useRouter()

    return (
      <div>
        {error.message}
        <button
          onClick={() => {
            // 使路由无效以重新加载加载器，同时重置错误边界
            router.invalidate()
          }}
        >
          重试
        </button>
      </div>
    )
  },
})
```

### 使用默认`ErrorComponent`

TanStack Router提供了一个默认的`ErrorComponent`，当路由加载或渲染生命周期中发生错误时渲染。如果你选择覆盖路由的错误组件，明智的做法是始终回退到使用默认`ErrorComponent`渲染任何未捕获的错误：

```tsx
// routes/posts.tsx
import { createFileRoute, ErrorComponent } from '@tanstack/react-router'

export const Route = createFileRoute('/posts')({
  loader: () => fetchPosts(),
  errorComponent: ({ error }) => {
    if (error instanceof MyCustomError) {
      // 渲染自定义错误信息
      return <div>{error.message}</div>
    }

    // 回退到默认ErrorComponent
    return <ErrorComponent error={error} />
  },
})
```
