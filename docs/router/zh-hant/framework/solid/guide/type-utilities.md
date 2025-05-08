---
source-updated-at: '2025-02-22T09:47:51.000Z'
translation-updated-at: '2025-05-08T20:15:37.655Z'
ref: docs/router/zh-hant/framework/react/guide/type-utilities.md
replace:
  react-router: solid-router
  React.ReactNode: Solid.JSX.Element
---

[//]: # 'TypeCheckingNavigateOptionsWithValidateNavigateOptionsImpl'

```tsx
export interface UseConditionalNavigateResult {
  enable: () => void
  disable: () => void
  navigate: () => void
}

export function useConditionalNavigate<
  TRouter extends RegisteredRouter,
  TOptions,
>(
  navigateOptions: ValidateNavigateOptions<TRouter, TOptions>,
): UseConditionalNavigateResult
export function useConditionalNavigate(
  navigateOptions: ValidateNavigateOptions,
): UseConditionalNavigateResult {
  const [enabled, setEnabled] = createSignal(false)
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
