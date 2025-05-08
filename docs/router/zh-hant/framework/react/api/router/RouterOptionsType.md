---
source-updated-at: '2025-02-28T11:38:02.000Z'
translation-updated-at: '2025-05-08T20:24:17.474Z'
id: RouterOptions
title: RouterOptions
---

`RouterOptions` 類型包含了所有可用於配置路由實例的選項。

## RouterOptions 屬性

`RouterOptions` 類型接受一個物件，該物件具有以下屬性和方法：

### `routeTree` 屬性

- 類型：`AnyRoute`
- 必填
- 用於配置路由實例的路由樹。

### `history` 屬性

- 類型：`RouterHistory`
- 選填
- 用於管理瀏覽器歷史記錄的 history 物件。如果未提供，將建立並使用一個新的 `createBrowserHistory` 實例。

### `stringifySearch` 方法

- 類型：`(search: Record<string, any>) => string`
- 選填
- 用於在生成連結時將搜尋參數字串化的函式。
- 預設為 `defaultStringifySearch`。

### `parseSearch` 方法

- 類型：`(search: string) => Record<string, any>`
- 選填
- 用於在解析當前位置時解析搜尋參數的函式。
- 預設為 `defaultParseSearch`。

### `search.strict` 屬性

- 類型：`boolean`
- 選填
- 預設為 `false`
- 配置如何處理未知的搜尋參數（即未被任何 `validateSearch` 返回的參數）。
- 如果為 `false`，將保留未知搜尋參數。
- 如果為 `true`，將移除未知搜尋參數。

### `defaultPreload` 屬性

- 類型：`undefined | false | 'intent' | 'viewport' | 'render'`
- 選填
- 預設為 `false`
- 如果為 `false`，路由預設不會以任何方式預載入。
- 如果為 `'intent'`，當用戶懸停在連結上或在 `<Link>` 上檢測到 `touchstart` 事件時，路由將預設預載入。
- 如果為 `'viewport'`，當路由位於瀏覽器的視口內時，路由將預設預載入。
- 如果為 `'render'`，當路由在 DOM 中渲染時，路由將預設預載入。

### `defaultPreloadDelay` 屬性

- 類型：`number`
- 選填
- 預設為 `50`
- 路由在預載入前必須懸停或觸碰的延遲時間（毫秒）。

### `defaultComponent` 屬性

- 類型：`RouteComponent`
- 選填
- 預設為 `Outlet`
- 如果未提供元件，路由應使用的預設 `component`。

### `defaultErrorComponent` 屬性

- 類型：`RouteComponent`
- 選填
- 預設為 `ErrorComponent`
- 如果未提供錯誤元件，路由應使用的預設 `errorComponent`。

### `defaultNotFoundComponent` 屬性

- 類型：`NotFoundRouteComponent`
- 選填
- 預設為 `NotFound`
- 如果未提供 notFound 元件，路由應使用的預設 `notFoundComponent`。

### `defaultPendingComponent` 屬性

- 類型：`RouteComponent`
- 選填
- 如果未提供 pending 元件，路由應使用的預設 `pendingComponent`。

### `defaultPendingMs` 屬性

- 類型：`number`
- 選填
- 預設為 `1000`
- 如果未提供 pendingMs，路由應使用的預設 `pendingMs`。

### `defaultPendingMinMs` 屬性

- 類型：`number`
- 選填
- 預設為 `500`
- 如果未提供 pendingMinMs，路由應使用的預設 `pendingMinMs`。

### `defaultStaleTime` 屬性

- 類型：`number`
- 選填
- 預設為 `0`
- 如果未提供 staleTime，路由應使用的預設 `staleTime`。

### `defaultPreloadStaleTime` 屬性

- 類型：`number`
- 選填
- 預設為 `30_000` 毫秒（30 秒）
- 如果未提供 preloadStaleTime，路由應使用的預設 `preloadStaleTime`。

### `defaultPreloadGcTime` 屬性

- 類型：`number`
- 選填
- 預設為 `routerOptions.defaultGcTime`，預設為 30 分鐘。
- 如果未提供 preloadGcTime，路由應使用的預設 `preloadGcTime`。

### `defaultGcTime` 屬性

- 類型：`number`
- 選填
- 預設為 30 分鐘。
- 如果未提供 gcTime，路由應使用的預設 `gcTime`。

### `defaultOnCatch` 屬性

- 類型：`(error: Error, errorInfo: ErrorInfo) => void`
- 選填
- 用於處理被 Router ErrorBoundary 捕獲的錯誤的預設 `onCatch` 處理器。

### `defaultViewTransition` 屬性

- 類型：`boolean | ViewTransitionOptions`
- 選填
- 如果為 `true`，路由導航將使用 `document.startViewTransition()` 呼叫。
- 如果為 [`ViewTransitionOptions`](./ViewTransitionOptionsType.md)，路由導航將使用 `document.startViewTransition({update, types})` 呼叫，其中 `types` 將是傳遞給 `ViewTransitionOptions["types"]` 的字串陣列。如果瀏覽器不支援 viewTransition 類型，導航將回退到正常的 `document.startTransition()`，與傳遞 `true` 時相同。
- 如果瀏覽器不支援此 API，此選項將被忽略。
- 有關此函式如何運作的更多資訊，請參閱 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document/startViewTransition)。
- 有關 viewTransition 類型的更多資訊，請參閱 [Google](https://developer.chrome.com/docs/web-platform/view-transitions/same-document#view-transition-types)。

### `defaultHashScrollIntoView` 屬性

- 類型：`boolean | ScrollIntoViewOptions`
- 選填
- 預設為 `true`，因此在位置提交到歷史記錄後，將滾動到與 hash 匹配的元素。
- 如果為 `false`，在位置提交到歷史記錄後，不會滾動到與 hash 匹配的元素。
- 如果提供了一個物件，它將作為選項傳遞給 `scrollIntoView` 方法。
- 有關 `ScrollIntoViewOptions` 的更多資訊，請參閱 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)。

### `caseSensitive` 屬性

- 類型：`boolean`
- 選填
- 預設為 `false`
- 如果為 `true`，所有路由將以區分大小寫的方式匹配。

### `basepath` 屬性

- 類型：`string`
- 選填
- 預設為 `/`
- 整個路由器的基礎路徑。這對於在子路徑下掛載路由實例非常有用。

### `context` 屬性

- 類型：`any`
- 選填或必填（如果根路由是使用 [`createRootRouteWithContext()`](./createRootRouteWithContextFunction.md) 建立的）。
- 將提供給路由樹中所有路由的根上下文。這可以用於向樹中的所有路由提供上下文，而無需單獨提供給每個路由。

### `dehydrate` 方法

- 類型：`() => TDehydrated`
- 選填
- 當路由器脫水時將呼叫的函式。此函式的返回值將被序列化並存儲在路由器的脫水狀態中。

### `hydrate` 方法

- 類型：`(dehydrated: TDehydrated) => void`
- 選填
- 當路由器水合時將呼叫的函式。此函式的返回值將被序列化並存儲在路由器的脫水狀態中。

### `routeMasks` 屬性

- 類型：`RouteMask[]`
- 選填
- 用於遮罩路由樹中路由的路由遮罩陣列。路由遮罩是指將路由顯示在與其配置匹配的路徑不同的路徑上，例如模態彈出視窗，當共享時將顯示模態的內容而不是模態的上下文。

### `unmaskOnReload` 屬性

- 類型：`boolean`
- 選填
- 預設為 `false`
- 如果為 `true`，路由遮罩預設將在頁面重新載入時移除。這可以通過在遮罩上設置 `unmaskOnReload` 選項或在 `Navigate` 選項中設置 `unmaskOnReload` 選項來覆蓋。

### `Wrap` 屬性

- 類型：`React.Component`
- 選填
- 用於包裝整個路由器的元件。這對於向整個路由器提供上下文非常有用。僅應使用非 DOM 渲染元件（如 providers），其他任何元件都會導致水合錯誤。

**範例**

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  Wrap: ({ children }) => {
    return <MyContext.Provider value={myContext}>{children}</MyContext>
  },
})
```

### `InnerWrap` 屬性

- 類型：`React.Component`
- 選填
- 用於包裝路由器內部內容的元件。這對於向路由器的內部內容提供上下文非常有用，同時還需要訪問路由器上下文和鉤子。僅應使用非 DOM 渲染元件（如 providers），其他任何元件都會導致水合錯誤。

**範例**

```tsx
import { createRouter } from '@tanstack/react-router'

const router = createRouter({
  // ...
  InnerWrap: ({ children }) => {
    const routerState = useRouterState()

    return (
      <MyContext.Provider value={myContext}>
        {children}
      </MyContext>
    )
  },
})
```

### `notFoundMode` 屬性

- 類型：`'root' | 'fuzzy'`
- 選填
- 預設為 `'fuzzy'`
- 此屬性控制 TanStack Router 在無法找到與當前位置匹配的路由時如何處理。有關更多資訊，請參閱 [未找到錯誤指南](../../guide/not-found-errors.md)。

### `notFoundRoute` 屬性

- **已棄用**
- 類型：`NotFoundRoute`
- 選填
- 用作路由樹每個分支的預設未找到路由的路由。這可以通過在分支的根路由上提供 `NotFoundRoute` 選項來覆蓋。

### `errorSerializer` 屬性

- 類型：[`RouterErrorSerializer`]
- 選填
- 用於確定錯誤如何在伺服器和客戶端之間序列化和反序列化的序列化器物件。

#### `errorSerializer.serialize` 方法

- 類型：`(err: unknown) => TSerializedError`
- 此方法用於定義錯誤在存儲在路由器的脫水狀態中時如何序列化。

#### `errorSerializer.deserialize` 方法

- 類型：`(err: TSerializedError) => unknown`
- 此方法用於定義錯誤如何從路由器的脫水狀態中反序列化。

### `trailingSlash` 屬性

- 類型：`'always' | 'never' | 'preserve'`
- 選填
- 預設為 `never`
- 配置如何處理尾部斜線。`'always'` 將在不存在時添加尾部斜線，`'never'` 將在存在時移除尾部斜線，`'preserve'` 將不修改尾部斜線。

### `pathParamsAllowedCharacters` 屬性

- 類型：`Array<';' | ':' | '@' | '&' | '=' | '+' | '$' | ','>`
- 選填
- 配置哪些 URI 字元允許在路徑參數中使用，這些字元通常會被 encodeURIComponent 轉義。

### `defaultStructuralSharing` 屬性

- 類型：`boolean`
- 選填
- 預設為 `false`
- 配置是否預設啟用結構化共享以進行細粒度選擇器。
- 有關更多資訊，請參閱 [渲染優化指南](../../guide/render-optimizations.md)。

### `defaultRemountDeps` 屬性

- 類型：

```tsx
type defaultRemountDeps = (opts: RemountDepsOptions) => any

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
- 一個預設函式，將被呼叫以確定路由元件是否應在導航後重新掛載。如果此函式返回的值與之前不同，則將重新掛載。
- 返回值需要是可 JSON 序列化的。
- 預設情況下，如果路由元件在導航後保持活動狀態，則不會重新掛載。

範例：  
如果你想配置在 `params` 變更時重新掛載所有路由元件，請使用：

```tsx
remountDeps: ({ params }) => params
```
