---
source-updated-at: 2025-02-28T11:39:08.000Z
translation-updated-at: 2025-04-05T03:28:47.000Z
title: 类型工具
id: type-utilities
---

TanStack Router 暴露的大多数类型属于内部实现，可能包含破坏性变更且不易使用。因此，TanStack Router 专门提供了一组面向外部使用的易用类型。这些类型在类型层面复现了路由运行时的类型安全特性，并允许灵活选择类型检查的介入位置。

## 使用 `ValidateLinkOptions` 校验链接选项

`ValidateLinkOptions` 会对对象字面量进行类型检查，确保其符合 `Link` 选项的推断要求。例如，当您有一个通用 `HeadingLink` 组件同时接收 `title` 属性和 `linkOptions` 时，该组件就能复用于任何导航场景：

```tsx
export interface HeaderLinkProps<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TOptions = unknown,
> {
  title: string
  linkOptions: ValidateLinkOptions<TRouter, TOptions>
}

export function HeadingLink<TRouter extends RegisteredRouter, TOptions>(
  props: HeaderLinkProps<TRouter, TOptions>,
): React.ReactNode
export function HeadingLink(props: HeaderLinkProps): React.ReactNode {
  return (
    <>
      <h1>{props.title}</h1>
      <Link {...props.linkOptions} />
    </>
  )
}
```

通过更宽松的重载签名可避免泛型实现中的类型断言。虽然所有类型参数都是可选的，但为了最佳 TypeScript 性能，公开签名应始终指定 `TRouter`，而推断点（如 `HeadingLink`）应使用 `TOptions` 来正确收窄 `params` 和 `search` 类型。

这使得以下用法能获得完全类型安全的 `linkOptions`：

```tsx
<HeadingLink title="Posts" linkOptions={{ to: '/posts' }} />
<HeadingLink title="Post" linkOptions={{ to: '/posts/$postId', params: {postId: 'postId'} }} />
```

## 使用 `ValidateLinkOptionsArray` 校验链接选项数组

所有导航类型工具都提供数组变体。`ValidateLinkOptionsArray` 可校验 `Link` 选项数组的类型。例如在通用 `Menu` 组件中，每个菜单项都是 `Link`：

```tsx
export interface MenuProps<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TItems extends ReadonlyArray<unknown> = ReadonlyArray<unknown>,
> {
  items: ValidateLinkOptionsArray<TRouter, TItems>
}

export function Menu<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TItems extends ReadonlyArray<unknown>,
>(props: MenuProps<TRouter, TItems>): React.ReactNode
export function Menu(props: MenuProps): React.ReactNode {
  return (
    <ul>
      {props.items.map((item) => (
        <li>
          <Link {...item} />
        </li>
      ))}
    </ul>
  )
}
```

这使得以下 `items` 属性完全类型安全：

```tsx
<Menu
  items={[
    { to: '/posts' },
    { to: '/posts/$postId', params: { postId: 'postId' } },
  ]}
/>
```

还可以通过 `ValidateFromPath` 工具固定数组中每个 `Link` 的 `from` 基准路径：

```tsx
export interface MenuProps<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TItems extends ReadonlyArray<unknown> = ReadonlyArray<unknown>,
  TFrom extends string = string,
> {
  from: ValidateFromPath<TRouter, TFrom>
  items: ValidateLinkOptionsArray<TRouter, TItems, TFrom>
}

export function Menu<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TItems extends ReadonlyArray<unknown>,
  TFrom extends string = string,
>(props: MenuProps<TRouter, TItems, TFrom>): React.ReactNode
export function Menu(props: MenuProps): React.ReactNode {
  return (
    <ul>
      {props.items.map((item) => (
        <li>
          <Link {...item} from={props.from} />
        </li>
      ))}
    </ul>
  )
}
```

最终实现相对于固定路径的类型安全导航：

```tsx
<Menu
  from="/posts"
  items={[{ to: '.' }, { to: './$postId', params: { postId: 'postId' } }]}
/>
```

## 使用 `ValidateRedirectOptions` 校验重定向选项

`ValidateRedirectOptions` 会校验对象字面量是否符合重定向选项要求。例如在通用 `fetchOrRedirect` 函数中，当请求失败时执行重定向：

```tsx
export async function fetchOrRedirect<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TOptions,
>(
  url: string,
  redirectOptions: ValidateRedirectOptions<TRouter, TOptions>,
): Promise<unknown>
export async function fetchOrRedirect(
  url: string,
  redirectOptions: ValidateRedirectOptions,
): Promise<unknown> {
  const response = await fetch(url)

  if (!response.ok && response.status === 401) {
    throw redirect(redirectOptions)
  }

  return await response.json()
}
```

这使得传入 `fetchOrRedirect` 的 `redirectOptions` 完全类型安全：

```tsx
fetchOrRedirect('http://example.com/', { to: '/login' })
```

## 使用 `ValidateNavigateOptions` 校验导航选项

`ValidateNavigateOptions` 会校验对象字面量是否符合导航选项要求。例如在自定义 Hook 中实现条件导航：

[//]: # 'TypeCheckingNavigateOptionsWithValidateNavigateOptionsImpl'

```tsx
export interface UseConditionalNavigateResult {
  enable: () => void
  disable: () => void
  navigate: () => void
}

export function useConditionalNavigate<
  TRouter extends RegisteredRouter = RegisteredRouter,
  TOptions,
>(
  navigateOptions: ValidateNavigateOptions<TRouter, TOptions>,
): UseConditionalNavigateResult
export function useConditionalNavigate(
  navigateOptions: ValidateNavigateOptions,
): UseConditionalNavigateResult {
  const [enabled, setEnabled] = useState(false)
  const navigate = useNavigate()
  return {
    enable: () => setEnabled(true),
    disable: () => setEnabled(false),
    navigate: () => {
      if (enabled) {
        navigate(navigateOptions)
      }
    },
  }
}
```

[//]: # 'TypeCheckingNavigateOptionsWithValidateNavigateOptionsImpl'

这使得传入 `useConditionalNavigate` 的 `navigateOptions` 完全类型安全，并能基于 React 状态控制导航：

```tsx
const { enable, disable, navigate } = useConditionalNavigate({
  to: '/posts/$postId',
  params: { postId: 'postId' },
})
```
