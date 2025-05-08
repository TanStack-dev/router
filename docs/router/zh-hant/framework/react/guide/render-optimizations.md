---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:18:35.814Z'
title: 渲染優化
---

TanStack Router 內建多項優化機制，確保元件僅在必要時重新渲染。這些優化包括：

## 結構化共享 (structural sharing)

TanStack Router 採用「結構化共享 (structural sharing)」技術，在重新渲染時盡可能保留參照穩定性。此技術特別適用於儲存在 URL 中的狀態（例如搜尋參數）。

舉例來說，假設有一個帶有 `foo` 和 `bar` 兩個搜尋參數的 `details` 路由，存取方式如下：

```tsx
const search = Route.useSearch()
```

當僅改變 `bar` 參數（從 `/details?foo=f1&bar=b1` 導航至 `/details?foo=f1&bar=b2` 時），`search.foo` 會保持參照穩定，只有 `search.bar` 會被替換。

## 細粒度選擇器 (fine-grained selectors)

您可以使用 `useRouterState`、`useSearch` 等鉤子來存取和訂閱路由狀態。若希望元件僅在特定路由狀態（例如部分搜尋參數）變更時重新渲染，可透過 `select` 屬性進行部分訂閱：

```tsx
// 當 `bar` 變更時，元件不會重新渲染
const foo = Route.useSearch({ select: ({ foo }) => foo })
```

### 結合結構化共享與細粒度選擇器

`select` 函式可對路由狀態執行各種計算，並回傳不同類型的值（例如物件）：

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      foo: search.foo,
      hello: `hello ${search.foo}`,
    }
  },
})
```

雖然此寫法可行，但由於每次呼叫 `select` 都會回傳新物件，將導致元件每次重新渲染。

透過前述的「結構化共享 (structural sharing)」技術可避免此問題。預設情況下，為保持向後相容性，結構化共享功能是關閉的（此設定可能在 v2 版變更）。

啟用細粒度選擇器的結構化共享有兩種方式：

#### 在路由選項中預設啟用：

```tsx
const router = createRouter({
  routeTree,
  defaultStructuralSharing: true,
})
```

#### 在鉤子使用時個別啟用：

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      foo: search.foo,
      hello: `hello ${search.foo}`,
    }
  },
  structuralSharing: true,
})
```

> [!IMPORTANT]
> 結構化共享僅適用於 JSON 相容資料。若啟用此功能，`select` 無法回傳類別實例等非 JSON 相容物件。

遵循 TanStack Router 的型別安全設計，若嘗試以下操作，TypeScript 將報錯：

```tsx
const result = Route.useSearch({
  select: (search) => {
    return {
      date: new Date(),
    }
  },
  structuralSharing: true,
})
```

若已在路由選項預設啟用結構化共享，可透過設定 `structuralSharing: false` 避免此錯誤。
