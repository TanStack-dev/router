---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:28:28.000Z
title: 数据变更
---

由于TanStack Router本身不存储或缓存数据，它在数据变更（mutation）中的作用微乎其微，仅限于响应外部变更事件可能引发的URL副作用。尽管如此，我们仍整理了一些您可能感兴趣的变更相关功能和实现这些功能的库。

寻找并使用支持以下功能的变更工具：

- 处理并缓存提交状态
- 同时支持本地和全局的乐观UI更新
- 内置钩子以支持数据失效（或自动处理失效）
- 同时处理多个进行中的变更
- 将变更状态组织为全局可访问资源
- 提交状态历史记录与垃圾回收

推荐库：

- [TanStack Query](https://tanstack.com/query/latest/docs/react/guides/mutations)
- [SWR](https://swr.vercel.app/)
- [RTK Query](https://redux-toolkit.js.org/rtk-query/overview)
- [urql](https://formidable.com/open-source/urql/)
- [Relay](https://relay.dev/)
- [Apollo](https://www.apollographql.com/docs/react/)

甚至包括：

- [Zustand](https://zustand-demo.pmnd.rs/)
- [Jotai](https://jotai.org/)
- [Recoil](https://recoiljs.org/)
- [Redux](https://redux.js.org/)

与数据获取类似，变更状态管理并非万能方案，您需要根据团队需求选择合适的工具。建议尝试多种方案以确定最佳选择。

> ⚠️ 还在阅读？提交状态的持久化是个有趣的话题。是否永久保留所有变更记录？如何确定清理时机？用户离开屏幕后又返回该如何处理？让我们深入探讨！

## 变更后使TanStack Router失效

TanStack Router内置短期缓存机制。虽然路由匹配卸载后不会保留任何数据，但若发生与Router存储数据相关的变更，当前路由匹配数据很可能变得过时。

当加载器相关数据发生变更时，可使用`router.invalidate`强制重新加载当前所有路由匹配：

```tsx
const router = useRouter()

const addTodo = async (todo: Todo) => {
  try {
    await api.addTodo()
    router.invalidate()
  } catch {
    //
  }
}
```

当前路由匹配的失效操作在后台执行，现有数据会持续提供服务直至新数据准备就绪，其行为类似于导航至新路由。

若需等待所有加载器完成后再失效，可向`router.invalidate`传入`{sync: true}`：

```tsx
const router = useRouter()

const addTodo = async (todo: Todo) => {
  try {
    await api.addTodo()
    await router.invalidate({ sync: true })
  } catch {
    //
  }
}
```

## 长期变更状态

无论使用何种变更库，变更通常会产生与其提交相关的状态。虽然多数变更属于"即发即忘"型，但某些变更状态具有更长生命周期——或是为了支持乐观UI，或是为了向用户提供提交状态反馈。多数状态管理器会正确保留这些提交状态并对外暴露，以便显示加载指示器、成功/错误消息等UI元素。

考虑以下交互场景：

- 用户导航至`/posts/123/edit`编辑文章
- 用户编辑123号文章成功后，在编辑器下方看到"文章已更新"的成功提示
- 用户跳转至`/posts`列表页
- 用户再次返回`/posts/123/edit`编辑页

若未在路由变更时通知变更管理库，原先的提交状态可能仍然存在，导致用户返回编辑页时仍看到**"文章已更新成功"**的提示。这显然不符合预期——我们本意并非永久保留该变更状态！

## 使用变更键

理想情况下，若变更库支持键控机制，可通过键值变化来重置变更状态：

```tsx
const routeApi = getRouteApi('/posts/$postId/edit')

function EditPost() {
  const { roomId } = routeApi.useParams()

  const sendMessageMutation = useCoolMutation({
    fn: sendMessage,
    // 当roomId变化时清除变更状态（包括所有提交状态）
    key: ['sendMessage', roomId],
  })

  // 发送多条消息
  const test = () => {
    sendMessageMutation.mutate({ roomId, message: 'Hello!' })
    sendMessageMutation.mutate({ roomId, message: 'How are you?' })
    sendMessageMutation.mutate({ roomId, message: 'Goodbye!' })
  }

  return (
    <>
      {sendMessageMutation.submissions.map((submission) => {
        return (
          <div>
            <div>{submission.status}</div>
            <div>{submission.message}</div>
          </div>
        )
      })}
    </>
  )
}
```

## 使用`router.subscribe`方法

对于不支持键控机制的库，通常需要在用户离开屏幕时手动重置变更状态。此时可利用TanStack Router的`invalidate`和`subscribe`方法在用户不再需要时清理变更状态。

`router.subscribe`方法用于订阅路由事件的回调函数。这里我们特别关注`onResolved`事件——该事件在*路径变更（非仅刷新）且最终完成解析*时触发。

这是重置旧变更状态的理想时机。示例：

```tsx
const router = createRouter()
const coolMutationCache = createCoolMutationCache()

const unsubscribeFn = router.subscribe('onResolved', () => {
  // 路由变化时重置变更状态
  coolMutationCache.clear()
})
```
