---
source-updated-at: '2025-02-28T11:39:08.000Z'
translation-updated-at: '2025-05-08T20:18:02.280Z'
id: type-utilities
title: 類型工具
---

TanStack Router 所公開的大多數類型屬於內部使用，可能會有破壞性變更且不一定易於使用。因此，TanStack Router 提供了一組專注於易用性的公開類型，旨在供外部使用。這些類型在類型層面上提供了與 TanStack Router 運行時概念相同的類型安全體驗，同時保留了在何處進行類型檢查的靈活性。

## 使用 `ValidateLinkOptions` 檢查連結選項的類型

`ValidateLinkOptions` 會對物件字面量類型進行檢查，確保它們在推斷位置符合 `Link` 選項的規範。例如，你可能有一個通用的 `HeadingLink` 元件，它接受 `title` 屬性和 `linkOptions`，目的是讓這個元件可以重複用於任何導航場景。

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

為了避免在泛型簽章中進行類型斷言，這裡使用了更寬鬆的 `HeadingLink` 重載。在實現 `HeadingLink` 時，使用不含類型參數的寬鬆簽章是避免類型斷言的簡單方法。

所有工具類型的參數都是可選的，但為了獲得最佳的 TypeScript 性能，公開簽章中的 `TRouter` 應始終明確指定。而 `TOptions` 應在推斷位置（如 `HeadingLink`）使用，以正確推斷 `linkOptions` 並縮小 `params` 和 `search` 的範圍。

這樣做的結果是，以下範例中的 `linkOptions` 是完全類型安全的：

```tsx
<HeadingLink title="Posts" linkOptions={{ to: '/posts' }} />
<HeadingLink title="Post" linkOptions={{ to: '/posts/$postId', params: {postId: 'postId'} }} />
```

## 使用 `ValidateLinkOptionsArray` 檢查連結選項陣列的類型

所有導航類型工具都有對應的陣列變體。`ValidateLinkOptionsArray` 可以對 `Link` 選項的陣列進行類型檢查。例如，你可能有一個通用的 `Menu` 元件，其中每個項目都是一個 `Link`。

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

這當然使得以下 `items` 屬性完全類型安全：

```tsx
<Menu
  items={[
    { to: '/posts' },
    { to: '/posts/$postId', params: { postId: 'postId' } },
  ]}
/>
```

也可以為陣列中的每個 `Link` 選項固定 `from`。這樣可以讓所有 `Menu` 項目相對於 `from` 進行導航。`ValidateFromPath` 工具可以提供對 `from` 的額外類型檢查。

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

`ValidateLinkOptionsArray` 允許通過提供額外的類型參數來固定 `from`。結果是一個類型安全的 `Link` 選項陣列，提供相對於 `from` 的導航。

```tsx
<Menu
  from="/posts"
  items={[{ to: '.' }, { to: './$postId', params: { postId: 'postId' } }]}
/>
```

## 使用 `ValidateRedirectOptions` 檢查重新導向選項的類型

`ValidateRedirectOptions` 會對物件字面量類型進行檢查，確保它們在推斷位置符合重新導向選項的規範。例如，你可能需要一個通用的 `fetchOrRedirect` 函式，它接受 `url` 和 `redirectOptions`，目的是在 `fetch` 失敗時進行重新導向。

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

這樣做的結果是傳遞給 `fetchOrRedirect` 的 `redirectOptions` 是完全類型安全的：

```tsx
fetchOrRedirect('http://example.com/', { to: '/login' })
```

## 使用 `ValidateNavigateOptions` 檢查導航選項的類型

`ValidateNavigateOptions` 會對物件字面量類型進行檢查，確保它們在推斷位置符合導航選項的規範。例如，你可能想寫一個自訂鉤子來啟用/停用導航。

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

這樣做的結果是傳遞給 `useConditionalNavigate` 的 `navigateOptions` 是完全類型安全的，我們可以根據 React 狀態來啟用/停用導航：

```tsx
const { enable, disable, navigate } = useConditionalNavigate({
  to: '/posts/$postId',
  params: { postId: 'postId' },
})
```
