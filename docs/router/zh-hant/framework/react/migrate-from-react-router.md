---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:15:08.974Z'
title: 從 React Router 遷移
toc: false
---

**_如果您的 UI 顯示空白，請打開控制台，您可能會看到類似 `cannot use 'useNavigate' outside of context` 的錯誤訊息。這表示仍有部分 React Router 的 API 被導入並引用，您需要找到並移除這些內容。最簡單的方法是直接解除安裝 `react-router-dom`，接著您的檔案中會出現 TypeScript 錯誤提示。這樣您就能知道需要將哪些內容改為 `@tanstack/react-router` 的導入。_**

這裡是 [範例儲存庫](https://github.com/Benanna2019/SickFitsForEveryone/tree/migrate-to-tanstack/router/React-Router)

- [ ] 安裝路由 - `npm i @tanstack/react-router`
- [ ] **選填：** 解除安裝 React Router 以取得導入相關的 TypeScript 錯誤提示。
  - 目前尚不清楚是否能進行漸進式遷移，但似乎可以同時存在多個路由提供者 (router providers)，不過這並非理想做法。
  - React Router 與 TanStack Router 的 API 非常相似，如果貴公司習慣以衝刺週期 (sprint cycle) 進行調整，很可能在一兩個週期內就能完成遷移。
- [ ] 為每個現有的 React Router 路由建立對應的路由設定
- [ ] 建立根路由 (root route)
- [ ] 建立路由實例 (router instance)
- [ ] 在 main.tsx 中加入全域模組
- [ ] 從 main.tsx 中移除所有 React Router 相關內容（`createBrowserRouter` 或 `BrowserRouter`）、`Routes` 和 `Route` 元件
- [ ] **選填：** 重構 `render` 函式以實現自訂設定/提供者 (providers) - 上述範例儲存庫中有相關範例 - 在 Supertokens 的案例中這是必要的。Supertoken 針對 React Router 有特定設定，而其他 React 實作則有不同的設定方式
- [ ] 設定 RouterProvider 並將路由實例作為屬性傳入
- [ ] 將所有 React Router 的 `Link` 元件替換為 `@tanstack/react-router` 的 `Link` 元件
  - [ ] 加入帶有字面路徑的 `to` 屬性
  - [ ] 在需要時加入 `params` 屬性，例如 `params={{ orderId: order.id }}`
- [ ] 將所有 React Router 的 `useNavigate` 鉤子替換為 `@tanstack/react-router` 的 `useNavigate` 鉤子
  - [ ] 視需要設定 `to` 屬性和 `params` 屬性
- [ ] 將所有 React Router 的 `Outlet` 替換為 `@tanstack/react-router` 的對應元件
- [ ] 如果您使用 React Router 的 `useSearchParams` 鉤子，請將搜尋參數 (search params) 的預設值移至路由定義中的 `validateSearch` 屬性
  - [ ] 不要使用 `useSearchParams` 鉤子，改用 `@tanstack/react-router` 的 `Link` 元件的 `search` 屬性來更新搜尋參數狀態
  - [ ] 讀取搜尋參數的方式如下：
    - `const { page } = useSearch({ from: productPage.fullPath })`
- [ ] 如果使用 React Router 的 `useParams` 鉤子，請將導入來源改為 `@tanstack/react-router`，並設定 `from` 屬性為您想讀取參數物件的字面路徑名稱
  - 假設我們有一個路徑名為 `orders/$orderid` 的路由。
  - 在 `useParams` 鉤子中，我們會這樣設定：`const params = useParams({ from: "/orders/$orderId" })`
  - 之後在任何需要存取 order id 的地方，我們都可以從參數物件中取得：`params.orderId`
