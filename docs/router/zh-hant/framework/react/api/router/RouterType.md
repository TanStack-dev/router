---
source-updated-at: '2025-03-31T22:13:23.000Z'
translation-updated-at: '2025-05-08T20:23:59.848Z'
id: RouterType
title: Router type
---

`Router` 類型用於描述路由器實例。

## `Router` 屬性與方法

`Router` 實例具有以下屬性和方法：

### `.update` 方法

- 類型: `(newOptions: RouterOptions) => void`
- 使用新選項更新路由器實例。

### `state` 屬性

- 類型: [`RouterState`](./RouterStateType.md)
- 路由器的當前狀態。

> ⚠️⚠️⚠️ **`router.state` 始終是最新的，但並非響應式 (reactive)。如果在元件中使用 `router.state`，當路由器狀態變更時，元件不會重新渲染。若要取得路由器狀態的響應式版本，請使用 [`useRouterState`](./useRouterStateHook.md) 鉤子 (hook)。**

### `.subscribe` 方法

- 類型: `(eventType: TType, fn: ListenerFn<RouterEvents[TType]>) => (event: RouterEvent) => void`
- 訂閱 [`RouterEvent`](./RouterEventsType.md)。
- 返回一個可用於取消訂閱事件的函式。
- 提供給返回函式的回調 (callback) 將在事件觸發時被呼叫。

### `.matchRoutes` 方法

- 類型: `(pathname: string, locationSearch: Record<string, any>, opts?: { throwOnError?: boolean; }) => RouteMatch[]`
- 將路徑名稱 (pathname) 和搜尋參數 (search params) 與路由器的路由樹 (route tree) 進行匹配，並返回路由匹配 (route matches) 的陣列。
- 如果 `opts.throwOnError` 為 `true`，匹配過程中發生的任何錯誤都會被拋出（同時也會在路由匹配的 `error` 屬性中返回）。

### `.cancelMatch` 方法

- 類型: `(matchId: string) => void`
- 透過呼叫 `match.abortController.abort()` 取消目前正在進行的路由匹配。

### `.cancelMatches` 方法

- 類型: `() => void`
- 透過對每個匹配呼叫 `match.abortController.abort()`，取消所有目前正在進行的路由匹配。

### `.buildLocation` 方法

建立一個新的解析後位置 (parsed location) 物件，可用於後續導航至新位置。

- 類型: `(opts: BuildNextOptions) => ParsedLocation`
- 屬性
  - `from`
    - 類型: `string`
    - 選填
    - 導航來源路徑。若未提供，則使用當前路徑。
  - `to`
    - 類型: `string | number | null`
    - 選填
    - 導航目標路徑。若為 `null`，則使用當前路徑。
  - `params`
    - 類型: `true | Updater<unknown>`
    - 選填
    - 若為 `true`，則使用當前參數。若提供函式，則會使用當前參數呼叫該函式，並使用其返回值。
  - `search`
    - 類型: `true | Updater<unknown>`
    - 選填
    - 若為 `true`，則使用當前搜尋參數。若提供函式，則會使用當前搜尋參數呼叫該函式，並使用其返回值。
  - `hash`
    - 類型: `true | Updater<string>`
    - 選填
    - 若為 `true`，則使用當前雜湊值 (hash)。若提供函式，則會使用當前雜湊值呼叫該函式，並使用其返回值。
  - `state`
    - 類型: `true | NonNullableUpdater<ParsedHistoryState, HistoryState>`
    - 選填
    - 若為 `true`，則使用當前狀態。若提供函式，則會使用當前狀態呼叫該函式，並使用其返回值。
  - `mask`
    - 類型: `object`
    - 選填
    - 包含所有相同的 BuildNextOptions，並額外包含 `unmaskOnReload`。
    - `unmaskOnReload`
      - 類型: `boolean`
      - 選填
      - 若為 `true`，則在重新載入頁面時移除路由遮罩 (route mask)。可透過在 `Navigate` 選項中設定 `unmaskOnReload` 來針對每次導航覆寫此行為。

### `.commitLocation` 方法

將新位置物件提交至瀏覽器歷史記錄。

- 類型
  ```tsx
  type commitLocation = (
    location: ParsedLocation & {
      replace?: boolean
      resetScroll?: boolean
      hashScrollIntoView?: boolean | ScrollIntoViewOptions
      ignoreBlocker?: boolean
    },
  ) => Promise<void>
  ```
- 屬性
  - `location`
    - 類型: [`ParsedLocation`](./ParsedLocationType.md)
    - 必填
    - 要提交至瀏覽器歷史記錄的位置。
  - `replace`
    - 類型: `boolean`
    - 選填
    - 預設為 `false`。
    - 若為 `true`，則使用 `history.replace` 而非 `history.push` 提交位置至瀏覽器歷史記錄。
  - `resetScroll`
    - 類型: `boolean`
    - 選填
    - 預設為 `true`，以便在位置提交至瀏覽器歷史記錄後將滾動位置重置為 0,0。
    - 若為 `false`，則在位置提交至歷史記錄後不會重置滾動位置。
  - `hashScrollIntoView`
    - 類型: `boolean | ScrollIntoViewOptions`
    - 選填
    - 預設為 `true`，以便在位置提交至歷史記錄後將符合雜湊值的元素滾動至視圖中。
    - 若為 `false`，則不會將符合雜湊值的元素滾動至視圖中。
    - 若提供物件，則會將其作為選項傳遞給 `scrollIntoView` 方法。
    - 有關 `ScrollIntoViewOptions` 的更多資訊，請參閱 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollIntoView)。
  - `ignoreBlocker`
    - 類型: `boolean`
    - 選填
    - 預設為 `false`。
    - 若為 `true`，則導航將忽略任何可能阻止它的攔截器 (blocker)。

### `.navigate` 方法

導航至新位置。

- 類型
  ```tsx
  type navigate = (options: NavigateOptions) => Promise<void>
  ```

### `.invalidate` 方法

透過強制重新呼叫其 `beforeLoad` 和 `load` 函式，使路由匹配失效。

- 類型: `(opts?: {filter?: (d: MakeRouteMatchUnion<TRouter>) => boolean, sync?: boolean}) => Promise<void>`
- 當載入器 (loader) 資料可能過時或失效時，此方法非常有用。例如，若有一個顯示文章列表的路由，且有一個從 API 獲取文章列表的載入器函式，則可能需要在每次建立新文章時使該路由的匹配失效，以確保文章列表始終是最新的。
- 若未提供 `filter`，則所有匹配都將失效。
- 若提供 `filter`，則僅對 `filter` 返回 `true` 的匹配進行失效。
- 若 `sync` 為 `true`，則此函式返回的 Promise 僅在所有載入器完成後才會解析。
- 若需強制重新觸發載入器，也可在命令式 (imperatively) `reset` 路由器的 `CatchBoundary` 時使路由器失效。

### `.clearCache` 方法

移除快取的路由匹配。

- 類型: `(opts?: {filter?: (d: MakeRouteMatchUnion<TRouter>) => boolean}) => void`
- 若未提供 `filter`，則所有快取的匹配都將被移除。
- 若提供 `filter`，則僅移除 `filter` 返回 `true` 的匹配。

### `.load` 方法

載入所有當前匹配的路由匹配，並在所有匹配載入完成且準備渲染時解析。

> ⚠️⚠️⚠️ **`router.load()` 會遵循 `route.staleTime`，若路由匹配仍為最新狀態，則不會強制重新載入。若需強制重新載入路由匹配，請改用 `router.invalidate()`。**

- 類型: `(opts?: {sync?: boolean}) => Promise<void>`
- 若 `sync` 為 `true`，則此函式返回的 Promise 僅在所有載入器完成後才會解析。
- 此方法最常見的用途是在進行伺服器端渲染 (SSR) 時呼叫，以確保在嘗試將應用程式串流或渲染至客戶端之前，已載入當前路由的所有關鍵資料。

### `.preloadRoute` 方法

預載入所有符合提供的 `NavigateOptions` 的匹配。

> ⚠️⚠️⚠️ **預載入的路由匹配不會長期儲存在路由器狀態中，僅會儲存至下一次嘗試的導航動作。**

- 類型: `(opts?: NavigateOptions) => Promise<RouteMatch[]>`
- 屬性
  - `opts`
    - 類型: `NavigateOptions`
    - 選填，預設為當前位置。
    - 用於決定要預載入哪些路由匹配的選項。
- 返回
  - 一個 Promise，解析為所有預載入的路由匹配的陣列。

### `.loadRouteChunk` 方法

載入路由的 JS 區塊 (chunk)。

- 類型: `(route: AnyRoute) => Promise<void>`

### `.matchRoute` 方法

將路徑名稱和搜尋參數與路由器的路由樹進行匹配，並返回路由匹配的參數，若未找到匹配則返回 `false`。

- 類型: `(dest: ToOptions, matchOpts?: MatchRouteOptions) => RouteMatch['params'] | false`
- 屬性
  - `dest`
    - 類型: `ToOptions`
    - 必填
    - 要匹配的目標。
  - `matchOpts`
    - 類型: `MatchRouteOptions`
    - 選填
    - 用於匹配目標的選項。
- 返回
  - 若找到匹配，則返回路由匹配的參數。
  - 若未找到匹配，則返回 `false`。

### `.dehydrate` 方法

將路由器的關鍵狀態脫水 (dehydrate) 為可序列化的物件，以便在初始請求中傳送至客戶端。

- 類型: `() => DehydratedRouter`
- 返回
  - 包含路由器關鍵狀態的可序列化物件。

### `.hydrate` 方法

從伺服器在初始請求中傳送的可序列化物件中，為路由器的關鍵狀態補水 (hydrate)。

- 類型: `(dehydrated: DehydratedRouter) => void`
- 屬性
  - `dehydrated`
    - 類型: `DehydratedRouter`
    - 必填
    - 從伺服器傳送的脫水路由器狀態。
