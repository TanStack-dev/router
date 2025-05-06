---
source-updated-at: 2025-02-22T09:47:51.000Z
translation-updated-at: 2025-04-05T03:26:00.000Z
title: 链接选项
---

您可能希望复用那些需要传递给 `Link`、`redirect` 或 `navigate` 的选项。这种情况下，您可能会认为使用对象字面量是表示传递给 `Link` 的选项的好方法。

```tsx
const dashboardLinkOptions = {
  to: '/dashboard',
  search: { search: '' },
}

function DashboardComponent() {
  return <Link {...dashboardLinkOptions} />
}
```

这里存在几个问题。`dashboardLinkOptions.to` 被推断为 `string` 类型，默认情况下传递给 `Link`、`navigate` 或 `redirect` 时会解析为每个路由（这个特定问题可以通过 `as const` 修复）。另一个问题是，我们只有在将 `dashboardLinkOptions` 展开到 `Link` 中时，才知道它是否通过类型检查。我们很容易创建错误的导航选项，只有在选项展开到 `Link` 中时才会发现类型错误。

### 使用 `linkOptions` 函数创建可复用的选项

`linkOptions` 是一个函数，用于对对象字面量进行类型检查，并按原样返回推断的输入。这提供了与 `Link` 完全相同的类型安全性，使得选项在使用前更容易维护和复用。上面的示例使用 `linkOptions` 后如下所示：

```tsx
const dashboardLinkOptions = linkOptions({
  to: '/dashboard',
  search: { search: '' },
})

function DashboardComponent() {
  return <Link {...dashboardLinkOptions} />
}
```

这样可以提前对 `dashboardLinkOptions` 进行类型检查，然后可以在任何地方复用：

```tsx
const dashboardLinkOptions = linkOptions({
  to: '/dashboard',
  search: { search: '' },
})

export const Route = createFileRoute('/dashboard')({
  component: DashboardComponent,
  validateSearch: (input) => ({ search: input.search }),
  beforeLoad: () => {
    // 可以在 redirect 中使用
    throw redirect(dashboardLinkOptions)
  },
})

function DashboardComponent() {
  const navigate = useNavigate()

  return (
    <div>
      {/** 可以在 navigate 中使用 */}
      <button onClick={() => navigate(dashboardLinkOptions)} />

      {/** 可以在 Link 中使用 */}
      <Link {...dashboardLinkOptions} />
    </div>
  )
}
```

### `linkOptions` 的数组

在创建导航时，您可能会遍历一个数组来构建导航栏。这种情况下，`linkOptions` 可以用于对一组对象字面量进行类型检查，这些对象字面量是用于 `Link` 属性的：

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

`linkOptions` 的输入会被推断并原样返回，如示例中使用的 `label` 所示，因为它并不存在于 `Link` 属性中。
