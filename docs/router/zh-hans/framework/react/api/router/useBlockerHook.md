---
source-updated-at: '2025-03-13T22:56:02.000Z'
translation-updated-at: '2025-05-06T17:36:45.576Z'
id: useBlockerHook
title: useBlocker hook
---

`useBlocker` 是一个钩子方法，当满足条件时会[阻止导航](../../guide/navigation-blocking.md)。

> ⚠️ 以下新的 `useBlocker` API 目前处于 _实验性_ 阶段。

## useBlocker 选项

`useBlocker` 钩子接受一个 _必填_ 参数，即选项对象：

### `options.shouldBlockFn` 选项

- 必填
- 类型：`ShouldBlockFn`
- 该函数应返回一个 `boolean` 或 `Promise<boolean>`，用于告知拦截器是否应阻止当前导航
- 函数接收类型为 `ShouldBlockFnArgs` 的参数，该参数包含当前路由、目标路由及执行操作的信息
- 可以理解为该函数告知路由是否应阻止导航，返回 `true` 表示阻止导航，`false` 表示允许导航

```ts
interface ShouldBlockFnLocation<...> {
  routeId: TRouteId
  fullPath: TFullPath
  pathname: string
  params: TAllParams
  search: TFullSearchSchema
}

type ShouldBlockFnArgs = {
  current: ShouldBlockFnLocation
  next: ShouldBlockFnLocation
  action: HistoryAction
}
```

### `options.disabled` 选项

- 可选 - 默认值为 `false`
- 类型：`boolean`
- 指定是否完全禁用拦截器

### `options.enableBeforeUnload` 选项

- 可选 - 默认值为 `true`
- 类型：`boolean | (() => boolean)`
- 控制拦截器是否有时或总是阻止浏览器的 `beforeUnload` 事件

### `options.withResolver` 选项

- 可选 - 默认值为 `false`
- 类型：`boolean`
- 指定是否使用钩子返回的解析器，或由 `shouldBlockFn` 函数自行解决拦截逻辑

### `options.blockerFn` 选项 (⚠️ 已弃用)

- 可选
- 类型：`BlockerFn`
- 返回 `boolean` 或 `Promise<boolean>` 的函数，指示是否允许导航。

### `options.condition` 选项 (⚠️ 已弃用)

- 可选 - 默认值为 `true`
- 类型：`boolean`
- 当该条件为 `true` 时，导航尝试会被阻止。

## useBlocker 返回值

返回一个对象，包含手动控制导航拦截与放行的功能。

- `status` - 字符串字面量，可能为 `'blocked'` 或 `'idle'`
- `next` - 当状态为 `blocked` 时，包含目标位置信息的类型可收缩对象
- `current` - 当状态为 `blocked` 时，包含当前位置信息的类型可收缩对象
- `action` - 当状态为 `blocked` 时，显示触发导航操作的 `HistoryAction` 字符串
- `proceed` - 当状态为 `blocked` 时，用于继续导航的函数
- `reset` - 当状态为 `blocked` 时，用于取消导航的函数（`status` 将重置为 `'idle'`）

或

当 `withResolver` 为 `false` 时返回 `void`

## 示例

`useBlocker` 钩子的两种常见用例：

### 基础用法

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => formIsDirty,
  })

  // ...
}
```

### 自定义 UI

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  const { proceed, reset, status, next } = useBlocker({
    shouldBlockFn: () => formIsDirty,
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>即将导航至 {next.pathname}</p>
          <p>确定要离开吗？</p>
          <button onClick={proceed}>是</button>
          <button onClick={reset}>否</button>
        </div>
      )}
    </>
}
```

### 条件拦截

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ next }) => {
      return !next.pathname.includes('step/')
    },
    withResolver: true,
  })

  // ...

  return (
    <>
      {/* ... */}
      {status === 'blocked' && (
        <div>
          <p>确定要离开吗？</p>
          <button onClick={proceed}>是</button>
          <button onClick={reset}>否</button>
        </div>
      )}
    </>
  )
}
```

### 不使用解析器

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: ({ next }) => {
      if (next.pathname.includes('step/')) {
        return false
      }

      const shouldLeave = confirm('确定要离开吗？')
      return !shouldLeave
    },
  })

  // ...
}
```

### 类型收缩

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  // 阻止从 editor-1 跳转到 /foo/123?hello=world
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ current, next }) => {
      if (
        current.routeId === '/editor-1' &&
        next.fullPath === '/foo/$id' &&
        next.params.id === '123' &&
        next.search.hello === 'world'
      ) {
        return true
      }
      return false
    },
    enableBeforeUnload: false,
    withResolver: true,
  })

  // ...
}
```
