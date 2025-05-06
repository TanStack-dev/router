---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:33:56.000Z
title: 导航阻止
---

导航阻止是一种防止导航发生的方式。通常在以下用户尝试导航的情况下使用：

- 存在未保存的更改
- 正在填写表单过程中
- 正在进行支付操作

在这些场景中，应向用户显示提示或自定义界面以确认是否要离开当前页面。

- 如果用户确认，导航将正常继续
- 如果用户取消，所有待处理的导航将被阻止

## 导航阻止的工作原理

导航阻止为底层历史API添加了一层或多层"拦截器"。当存在任何拦截器时，导航将通过以下方式之一暂停：

- 自定义UI
  - 如果导航由路由层级可控的触发源引起，允许执行任何任务或向用户展示任何确认操作的UI。每个拦截器的`blocker`函数将按顺序异步执行。若任一拦截器函数返回或解析为`true`，则允许导航，其他拦截器继续执行直到全部通过。若任一拦截器返回或解析为`false`，则取消导航并忽略剩余`blocker`函数。
- `onbeforeunload`事件
  - 对于无法直接控制的页面事件，依赖浏览器的`onbeforeunload`事件。当用户尝试关闭标签页/窗口、刷新或以任何方式卸载页面资源时，浏览器将显示通用提示"确定要离开吗？"。用户确认则绕过所有拦截器卸载页面，取消则保持当前页面状态。

## 如何使用导航阻止

有两种使用方式：

- 基于钩子/逻辑的阻止
- 基于组件的阻止

## 基于钩子/逻辑的阻止

例如阻止表单存在未保存更改时的导航：

[//]: # 'HookBasedBlockingExample'

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => {
      if (!formIsDirty) return false

      const shouldLeave = confirm('确定要离开吗？')
      return !shouldLeave
    },
  })

  // ...
}
```

[//]: # 'HookBasedBlockingExample'

`shouldBlockFn`提供对`current`和`next`位置的类型安全访问：

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  // 始终阻止从/foo跳转到/bar/123?hello=world
  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: ({ current, next }) => {
      return (
        current.routeId === '/foo' &&
        next.fullPath === '/bar/$id' &&
        next.params.id === 123 &&
        next.search.hello === 'world'
      )
    },
    withResolver: true,
  })

  // ...
}
```

更多`useBlocker`钩子信息参见[API参考](../api/router/useBlockerHook.md)。

## 基于组件的阻止

除钩子方式外，可使用`Block`组件实现相同效果：

[//]: # 'ComponentBasedBlockingExample'

```tsx
import { Block } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  return (
    <Block
      shouldBlockFn={() => {
        if (!formIsDirty) return false

        const shouldLeave = confirm('确定要离开吗？')
        return !shouldLeave
      }}
    />
  )

  // 或

  return (
    <Block shouldBlockFn={() => !formIsDirty} withResolver>
      {({ status, proceed, reset }) => <>{/* ... */}</>}
    </Block>
  )
}
```

[//]: # 'ComponentBasedBlockingExample'

## 如何展示自定义UI？

多数情况下，在钩子中使用`window.confirm`配合`withResolver: false`即可明确提示用户。但在需要更贴合应用设计的非侵入式UI时：

**注意：** 当`withResolver`为`true`时，`shouldBlockFn`的返回值不会直接决定阻止结果。

### 带解析器的钩子自定义UI

[//]: # 'HookBasedCustomUIBlockingWithResolverExample'

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  const { proceed, reset, status } = useBlocker({
    shouldBlockFn: () => formIsDirty,
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
}
```

[//]: # 'HookBasedCustomUIBlockingWithResolverExample'

### 不带解析器的钩子自定义UI

[//]: # 'HookBasedCustomUIBlockingWithoutResolverExample'

```tsx
import { useBlocker } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  useBlocker({
    shouldBlockFn: () => {
      if (!formIsDirty) {
        return false
      }

      const shouldBlock = new Promise<boolean>((resolve) => {
        // 使用自选的模态管理器
        modals.open({
          title: '确定要离开吗？',
          children: (
            <SaveBlocker
              confirm={() => {
                modals.closeAll()
                resolve(false)
              }}
              reject={() => {
                modals.closeAll()
                resolve(true)
              }}
            />
          ),
          onClose: () => resolve(true),
        })
      })
      return shouldBlock
    },
  })

  // ...
}
```

[//]: # 'HookBasedCustomUIBlockingWithoutResolverExample'

### 基于组件的自定义UI

`Block`组件通过渲染属性提供相同状态和函数：

[//]: # 'ComponentBasedCustomUIBlockingExample'

```tsx
import { Block } from '@tanstack/react-router'

function MyComponent() {
  const [formIsDirty, setFormIsDirty] = useState(false)

  return (
    <Block shouldBlockFn={() => formIsDirty} withResolver>
      {({ status, proceed, reset }) => (
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
      )}
    </Block>
  )
}
```

[//]: # 'ComponentBasedCustomUIBlockingExample'
