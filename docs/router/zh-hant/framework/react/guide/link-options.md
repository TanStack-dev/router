---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:19:31.552Z'
title: 連結選項
---

# 連結選項 (Link Options)

你可能會想重複使用那些要傳遞給 `Link`、`redirect` 或 `navigate` 的選項。在這種情況下，你可能會認為物件字面值 (object literal) 是表示傳遞給 `Link` 選項的好方法。

```tsx
const dashboardLinkOptions = {
  to: '/dashboard',
  search: { search: '' },
}

function DashboardComponent() {
  return <Link {...dashboardLinkOptions} />
}
```

這裡有幾個問題。`dashboardLinkOptions.to` 被推論為 `string`，預設情況下當傳遞給 `Link`、`navigate` 或 `redirect` 時會解析為每個路由（這個特定問題可以透過 `as const` 修復）。另一個問題是我們不知道 `dashboardLinkOptions` 是否通過類型檢查，直到它被展開 (spread) 到 `Link` 中。我們很容易建立錯誤的導航選項，只有當選項被展開到 `Link` 時，我們才會知道有類型錯誤。

### 使用 `linkOptions` 函式建立可重複使用的選項

`linkOptions` 是一個函式，它會對物件字面值進行類型檢查，並回傳推論後的輸入內容。這提供了與 `Link` 完全相同的類型安全檢查，讓選項在使用前就能確保正確性，便於維護和重複使用。我們上面的範例使用 `linkOptions` 看起來像這樣：

```tsx
const dashboardLinkOptions = linkOptions({
  to: '/dashboard',
  search: { search: '' },
})

function DashboardComponent() {
  return <Link {...dashboardLinkOptions} />
}
```

這樣可以對 `dashboardLinkOptions` 進行預先類型檢查，然後在任何地方重複使用

```tsx
const dashboardLinkOptions = linkOptions({
  to: '/dashboard',
  search: { search: '' },
})

export const Route = createFileRoute('/dashboard')({
  component: DashboardComponent,
  validateSearch: (input) => ({ search: input.search }),
  beforeLoad: () => {
    // 可以用在 redirect
    throw redirect(dashboardLinkOptions)
  },
})

function DashboardComponent() {
  const navigate = useNavigate()

  return (
    <div>
      {/** 可以用在 navigate */}
      <button onClick={() => navigate(dashboardLinkOptions)} />

      {/** 可以用在 Link */}
      <Link {...dashboardLinkOptions} />
    </div>
  )
}
```

### `linkOptions` 陣列

在建立導航時，你可能會遍歷一個陣列來建構導航列。在這種情況下，`linkOptions` 可以用來對預定用於 `Link` 屬性的物件字面值陣列進行類型檢查

```tsx
const options = linkOptions([
  {
    to: '/dashboard',
    label: 'Summary',
    activeOptions: { exact: true },
  },
  {
    to: '/dashboard/invoices',
    label: 'Invoices',
  },
  {
    to: '/dashboard/users',
    label: 'Users',
  },
])

function DashboardComponent() {
  return (
    <>
      <div className="flex items-center border-b">
        <h2 className="text-xl p-2">Dashboard</h2>
      </div>

      <div className="flex flex-wrap divide-x">
        {options.map((option) => {
          return (
            <Link
              {...option}
              key={option.to}
              activeProps={{ className: `font-bold` }}
              className="p-2"
            >
              {option.label}
            </Link>
          )
        })}
      </div>
      <hr />

      <Outlet />
    </>
  )
}
```

`linkOptions` 的輸入會被推論並回傳，如使用 `label` 所示，因為這不存在於 `Link` 屬性上
