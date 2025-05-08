---
source-updated-at: '2025-02-08T21:36:40.000Z'
translation-updated-at: '2025-05-08T20:21:02.035Z'
id: useParamsHook
title: useParams hook
---

`useParams` 方法會返回與最接近的匹配及其所有父級匹配解析後的所有路徑參數。

## useParams 選項

`useParams` 掛鉤 (hook) 接受一個可選的 `options` 物件。

### `opts.strict` 選項

- 類型: `boolean`
- 可選 - `預設值: true`
- 如果設為 `false`，將忽略 `opts.from` 選項，並且類型會放寬為 `Partial<AllParams>`，以反映所有參數的共享類型。

### `opts.shouldThrow` 選項

- 類型: `boolean`
- 可選
- `預設值: true`
- 如果設為 `false`，當在當前渲染的匹配中未找到匹配項時，`useParams` 不會拋出不變異例外 (invariant exception)；在這種情況下，它會返回 `undefined`。

### `opts.select` 選項

- 可選
- `(params: AllParams) => TSelected`
- 如果提供，此函式將以參數物件調用，其返回值將從 `useParams` 返回。此值還將用於通過淺層相等性檢查 (shallow equality checks) 來決定掛鉤是否應重新渲染其父組件。

### `opts.structuralSharing` 選項

- 類型: `boolean`
- 可選
- 配置是否為 `select` 返回的值啟用結構共享 (structural sharing)。
- 更多資訊請參閱[渲染優化指南](../../guide/render-optimizations.md)。

## useParams 返回值

- 匹配及其父級匹配的路徑參數物件，如果提供了 `select` 函式，則返回 `TSelected`。

## 範例

```tsx
import { useParams } from '@tanstack/react-router'

const routeApi = getRouteApi('/posts/$postId')

function Component() {
  const params = useParams({ from: '/posts/$postId' })

  // 或

  const routeParams = routeApi.useParams()

  // 或

  const postId = useParams({
    from: '/posts/$postId',
    select: (params) => params.postId,
  })

  // 或

  const looseParams = useParams({ strict: false })

  // ...
}
```
