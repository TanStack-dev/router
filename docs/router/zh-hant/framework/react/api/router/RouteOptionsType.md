---
source-updated-at: '2025-02-28T11:38:02.000Z'
translation-updated-at: '2025-05-08T20:24:50.037Z'
id: RouteOptionsType
title: RouteOptions type
---

`RouteOptions` 類型用於描述建立路由時可使用的選項。

## RouteOptions 屬性

`RouteOptions` 類型接受一個包含以下屬性的物件：

### `getParentRoute` 方法

- 類型：`() => TParentRoute`
- 必填
- 此函式會回傳正在建立之路由的父路由。這是為了提供子路由配置完整的類型安全性，並確保路由樹的正確建構。

### `path` 屬性

- 類型：`string`
- 必填（除非提供 `id` 將路由配置為無路徑的佈局路由）
- 用於匹配路由的路徑片段。

### `id` 屬性

- 類型：`string`
- 選填，但若未提供 `path` 則為必填
- 若路由需配置為無路徑的佈局路由，此為其唯一識別碼。若提供此屬性，路由將不會匹配位置路徑名稱，其子路由將被扁平化至父路由進行匹配。

### `component` 屬性

- 類型：`RouteComponent` 或 `LazyRouteComponent`
- 選填 - 預設為 `<Outlet />`
- 當路由匹配時要渲染的內容。

### `errorComponent` 屬性

- 類型：`RouteComponent` 或 `LazyRouteComponent`
- 選填 - 預設為 `routerOptions.defaultErrorComponent`
- 當路由發生錯誤時要渲染的內容。

### `pendingComponent` 屬性

- 類型：`RouteComponent` 或 `LazyRouteComponent`
- 選填 - 預設為 `routerOptions.defaultPendingComponent`
- 當路由處於擱置狀態且達到其 `pendingMs` 閾值時要渲染的內容。

### `notFoundComponent` 屬性

- 類型：`NotFoundRouteComponent` 或 `LazyRouteComponent`
- 選填 - 預設為 `routerOptions.defaultNotFoundComponent`
- 當路由未找到時要渲染的內容。

### `validateSearch` 方法

- 類型：`(rawSearchParams: unknown) => TSearchSchema`
- 選填
- 此函式會在路由匹配時被呼叫，並傳入當前位置的原始搜尋參數，回傳驗證後的解析搜尋參數。若此函式拋出錯誤，路由將進入錯誤狀態，並在渲染時拋出錯誤。若未拋出錯誤，其回傳值將作為路由的搜尋參數使用，且回傳類型將被推斷至路由器的其餘部分。
- 可選地，參數類型可使用 `SearchSchemaInput` 類型標記，例如：`(searchParams: TSearchSchemaInput & SearchSchemaInput) => TSearchSchema`。若存在此標記，`TSearchSchemaInput` 將用於為 `<Link />` 和 `navigate()` 的 `search` 屬性提供類型（而非 `TSearchSchema`）。`TSearchSchemaInput` 與 `TSearchSchema` 之間的差異可用於表達可選的搜尋參數等情境。

### `search.middlewares` 屬性

- 類型：`(({search: TSearchSchema, next: (newSearch: TSearchSchema) => TSearchSchema}) => TSearchSchema)[]`
- 選填
- 搜尋中介軟體是在為路由或其子路由產生新連結時轉換搜尋參數的函式。
- 搜尋中介軟體會傳入當前的搜尋參數（若其為第一個執行的中介軟體），或由前一個中介軟體呼叫 `next` 來觸發。

### `parseParams` 方法 (⚠️ 已棄用)

- 類型：`(rawParams: Record<string, string>) => TParams`
- 選填
- 此函式會在路由匹配時被呼叫，並傳入當前位置的原始參數，回傳驗證後的解析參數。若此函式拋出錯誤，路由將進入錯誤狀態，並在渲染時拋出錯誤。若未拋出錯誤，其回傳值將作為路由的參數使用，且回傳類型將被推斷至路由器的其餘部分。

### `stringifyParams` 方法 (⚠️ 已棄用)

- 類型：`(params: TParams) => Record<string, string>`
- 若提供 `parseParams` 則為必填
- 此函式會在路由的解析參數被用於建構位置時呼叫。此函式應回傳一個有效的 `Record<string, string>` 映射物件。

### `params.parse` 方法

- 類型：`(rawParams: Record<string, string>) => TParams`
- 選填
- 此函式會在路由匹配時被呼叫，並傳入當前位置的原始參數，回傳驗證後的解析參數。若此函式拋出錯誤，路由將進入錯誤狀態，並在渲染時拋出錯誤。若未拋出錯誤，其回傳值將作為路由的參數使用，且回傳類型將被推斷至路由器的其餘部分。

### `params.stringify` 方法

- 類型：`(params: TParams) => Record<string, string>`
- 此函式會在路由的解析參數被用於建構位置時呼叫。此函式應回傳一個有效的 `Record<string, string>` 映射物件。

### `beforeLoad` 方法

- 類型：

```tsx
type beforeLoad = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TParentContext
    location: ParsedLocation
    navigate: NavigateFn<AnyRoute> // @deprecated
    buildLocation: BuildLocationFn<AnyRoute>
    cause: 'enter' | 'stay'
  },
) => Promise<TRouteContext> | TRouteContext | void
```

- 選填
- [`ParsedLocation`](./ParsedLocationType.md)
- 此非同步函式會在路由載入前呼叫。若在此拋出錯誤，路由的載入器將不會被呼叫，且路由不會渲染。若在導航期間拋出錯誤，導航將被取消，且錯誤會傳遞至 `onError` 函式。若在預載事件期間拋出錯誤，錯誤將被記錄至控制台，且預載會失敗。
- 若此函式回傳一個 Promise，路由將進入擱置狀態，並導致渲染暫停直到 Promise 解析。若此路由的 `pendingMs` 閾值達到，`pendingComponent` 將顯示直到解析完成。若 Promise 拒絕，路由將進入錯誤狀態，並在渲染時拋出錯誤。
- 若此函式回傳一個 `TRouteContext` 物件，該物件將合併至路由的上下文，並可在 `loader` 及其他相關路由元件/方法中使用。
- 常見用途是檢查使用者是否已驗證，若未驗證則將其重新導向至登入頁面。為此，您可以從此函式回傳或拋出一個 `redirect` 物件。

> 🚧 `opts.navigate` 已被棄用，將在下一個主要版本中移除。請改用 `throw redirect({ to: '/somewhere' })`。詳見 [`redirect` 函式文件](./redirectFunction.md)。

### `loader` 方法

- 類型：

```tsx
type loader = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TAllContext
    location: ParsedLocation
    navigate: NavigateFn<AnyRoute> // @deprecated
    buildLocation: BuildLocationFn<AnyRoute>
    cause: 'enter' | 'stay'
  },
) => Promise<TLoaderData> | TLoaderData | void
```

- 選填
- [`ParsedLocation`](./ParsedLocationType.md)
- 此非同步函式會在路由匹配時呼叫，並傳入路由的匹配物件。若在此拋出錯誤，路由將進入錯誤狀態，並在渲染時拋出錯誤。若在導航期間拋出錯誤，導航將被取消，且錯誤會傳遞至 `onError` 函式。若在預載事件期間拋出錯誤，錯誤將被記錄至控制台，且預載會失敗。
- 若此函式回傳一個 Promise，路由將進入擱置狀態，並導致渲染暫停直到 Promise 解析。若此路由的 `pendingMs` 閾值達到，`pendingComponent` 將顯示直到解析完成。若 Promise 拒絕，路由將進入錯誤狀態，並在渲染時拋出錯誤。
- 若此函式回傳一個 `TLoaderData` 物件，該物件將儲存在路由匹配上，直到該匹配不再作用。在另一個 `<Outlet />` 渲染前，可透過 `useLoaderData` 鉤子在路由匹配下的任何子元件中存取此資料。

> 🚧 `opts.navigate` 已被棄用，將在下一個主要版本中移除。請改用 `throw redirect({ to: '/somewhere' })`。詳見 [`redirect` 函式文件](./redirectFunction.md)。

### `loaderDeps` 方法

- 類型：

```tsx
type loaderDeps = (opts: { search: TFullSearchSchema }) => Record<string, any>
```

- 選填
- 此函式會在路由匹配前呼叫，為路由匹配提供額外的唯一識別，並作為匹配應重新載入的依賴追蹤器。它應回傳任何可序列化的值，用於在導航間唯一識別路由匹配。
- 預設情況下，路徑參數已用於唯一識別路由匹配，因此無需在此回傳這些參數。
- 若您的路由匹配依賴搜尋參數進行唯一識別，則必須在此回傳這些參數，以便它們可在 `loader` 的 `deps` 參數中使用。

### `staleTime` 屬性

- 類型：`number`
- 選填
- 預設為 `routerOptions.defaultStaleTime`，其預設為 `0`
- 路由匹配的載入器資料被視為新鮮的時間（毫秒）。若路由匹配在此時間內再次匹配，其載入器資料將不會重新載入。

### `preloadStaleTime` 屬性

- 類型：`number`
- 選填
- 預設為 `routerOptions.defaultPreloadStaleTime`，其預設為 `30_000` 毫秒（30 秒）
- 路由匹配的載入器資料在預載時被視為新鮮的時間（毫秒）。若路由匹配在此時間內再次預載，其載入器資料將不會重新載入。若路由匹配在此時間內載入（用於導航），則使用正常的 `staleTime`。

### `gcTime` 屬性

- 類型：`number`
- 選填
- 預設為 `routerOptions.defaultGcTime`，其預設為 30 分鐘。
- 路由匹配的載入器資料在預載後或不再使用時保留在記憶體中的時間（毫秒）。

### `shouldReload` 屬性

- 類型：`boolean | ((args: LoaderArgs) => boolean)`
- 選填
- 若為 `false` 或回傳 `false`，路由匹配的載入器資料將不會在後續匹配時重新載入。
- 若為 `true` 或回傳 `true`，路由匹配的載入器資料將在後續匹配時重新載入。
- 若為 `undefined` 或回傳 `undefined`，路由匹配的載入器資料將遵循預設的「過期時重新驗證」行為。

### `caseSensitive` 屬性

- 類型：`boolean`
- 選填
- 若為 `true`，此路由將以區分大小寫的方式進行匹配。

### `wrapInSuspense` 屬性

- 類型：`boolean`
- 選填
- 若為 `true`，此路由將強制包裹在 Suspense 邊界中，無論從其提供的元件檢查中是否找到這樣做的理由。

### `pendingMs` 屬性

- 類型：`number`
- 選填
- 預設為 `routerOptions.defaultPendingMs`，其預設為 `1000`
- 路由在顯示 `pendingComponent` 前必須處於擱置狀態的閾值時間（毫秒）。

### `pendingMinMs` 屬性

- 類型：`number`
- 選填
- 預設為 `routerOptions.defaultPendingMinMs`，其預設為 `500`
- 若顯示擱置元件，其將顯示的最短時間（毫秒）。這有助於避免擱置元件在螢幕上閃爍。

### `preloadMaxAge` 屬性

- 類型：`number`
- 選填
- 預設為 `30_000` 毫秒（30 秒）
- 路由的預載路由資料快取的最長時間（毫秒）。若路由在此時間內未匹配，其載入器資料將被捨棄。

### `preSearchFilters` 屬性 (⚠️ 已棄用，請改用 `search.middlewares`)

- 類型：`((search: TFullSearchSchema) => TFullSearchSchema)[]`
- 選填
- 此函式陣列會在產生到此路由或其子路由的任何新連結時呼叫。
- 每個函式會傳入當前的搜尋參數，並應回傳一個新的搜尋參數物件，用於產生連結。
- 其具有 `pre` 前綴，因為它會在傳遞給 `navigate`/`Link` 等的使用者提供函式有機會修改搜尋參數前呼叫。

### `postSearchFilters` 屬性 (⚠️ 已棄用，請改用 `search.middlewares`)

- 類型：`((search: TFullSearchSchema) => TFullSearchSchema)[]`
- 選填
- 此函式陣列會在產生到此路由或其子路由的任何新連結時呼叫。
- 每個函式會傳入當前的搜尋參數，並應回傳一個新的搜尋參數物件，用於產生連結。
- 其具有 `post` 前綴，因為它會在傳遞給 `navigate`/`Link` 等的使用者提供函式修改搜尋參數後呼叫。

### `onError` 屬性

- 類型：`(error: any) => void`
- 選填
- 此函式會在導航或預載事件期間拋出錯誤時呼叫。
- 若此函式拋出一個 [`redirect`](./redirectFunction.md)，路由器將立即處理並套用此重新導向。

### `onEnter` 屬性

- 類型：`(match: RouteMatch) => void`
- 選填
- 此函式會在路由匹配並載入（且在前一個位置未匹配時）時呼叫。

### `onStay` 屬性

- 類型：`(match: RouteMatch) => void`
- 選填
- 此函式會在路由匹配並載入（且在前一個位置已匹配時）時呼叫。

### `onLeave` 屬性

- 類型：`(match: RouteMatch) => void`
- 選填
- 此函式會在路由不再匹配（且在前一個位置已匹配時）時呼叫。

### `onCatch` 屬性

- 類型：`(error: Error, errorInfo: ErrorInfo) => void`
- 選填 - 預設為 `routerOptions.defaultOnCatch`
- 此函式會在路由遇到錯誤並捕獲錯誤時呼叫。

### `remountDeps` 方法

- 類型：

```tsx
type remountDeps = (opts: RemountDepsOptions) => any

interface RemountDepsOptions<
  in out TRouteId,
  in out TFullSearchSchema,
  in out TAllParams,
  in out TLoaderDeps,
> {
  routeId: TRouteId
  search: TFullSearchSchema
  params: TAllParams
  loaderDeps: TLoaderDeps
}
```

- 選填
- 此函式用於決定路由元件是否應在導航後重新掛載。若此函式回傳的值與先前不同，則會重新掛載。
- 回傳值需為可 JSON 序列化的。
- 預設情況下，若路由元件在導航後仍保持作用，則不會重新掛載。

範例：  
若您想在 `params` 變更時重新掛載路由元件，請使用：

```tsx
remountDeps: ({ params }) => params
```
