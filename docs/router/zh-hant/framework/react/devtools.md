---
source-updated-at: '2025-04-08T11:02:00.000Z'
translation-updated-at: '2025-05-08T20:15:50.003Z'
title: 開發工具
---

> 連結啊，帶上這把劍... 我是說開發者工具 (Devtools)！... 助你一路順遂！

揮舞雙手歡呼吧，因為 TanStack Router 附帶專屬的開發者工具 (Devtools)！ 🥳

當你開始 TanStack Router 的旅程時，你會希望這些開發者工具 (Devtools) 伴隨左右。它們能視覺化呈現 TanStack Router 的所有內部運作，若你陷入困境，很可能會為你節省數小時的除錯時間！

## 安裝

開發者工具 (Devtools) 是一個獨立的套件，需要額外安裝：

```sh
npm install @tanstack/react-router-devtools
```

或

```sh
pnpm add @tanstack/react-router-devtools
```

或

```sh
yarn add @tanstack/react-router-devtools
```

或

```sh
bun add @tanstack/react-router-devtools
```

## 導入開發者工具 (Devtools)

```js
import { TanStackRouterDevtools } from '@tanstack/react-router-devtools'
```

## 在生產環境中使用開發者工具 (Devtools)

若以 `TanStackRouterDevtools` 導入，開發者工具 (Devtools) 在生產環境中不會顯示。若你希望在 `process.env.NODE_ENV === 'production'` 的環境中使用開發者工具 (Devtools)，請改用 `TanStackRouterDevtoolsInProd`，它提供完全相同的選項：

```tsx
import { TanStackRouterDevtoolsInProd } from '@tanstack/react-router-devtools'
```

## 在 `RouterProvider` 內部使用

最簡單的方式是將開發者工具 (Devtools) 渲染在你的根路由 (或任何其他路由) 內部。這會自動將開發者工具 (Devtools) 連接到路由器實例。

```tsx
const rootRoute = createRootRoute({
  component: () => (
    <>
      <Outlet />
      <TanStackRouterDevtools />
    </>
  ),
})

const routeTree = rootRoute.addChildren([
  // ... 其他路由
])

const router = createRouter({
  routeTree,
})

function App() {
  return <RouterProvider router={router} />
}
```

## 手動傳遞路由器實例

若在 `RouterProvider` 內部渲染開發者工具 (Devtools) 不合你的胃口，開發者工具 (Devtools) 的 `router` 屬性可接受與傳遞給 `Router` 元件相同的 `router` 實例。這讓你能將開發者工具 (Devtools) 放在頁面上的任何位置，而不僅限於提供者 (Provider) 內部：

```tsx
function App() {
  return (
    <>
      <RouterProvider router={router} />
      <TanStackRouterDevtools router={router} />
    </>
  )
}
```

## 浮動模式 (Floating Mode)

浮動模式 (Floating Mode) 會將開發者工具 (Devtools) 作為一個固定的浮動元件掛載在你的應用中，並在螢幕角落提供一個切換按鈕來顯示或隱藏開發者工具 (Devtools)。此切換狀態會儲存在 localStorage 中，並在重新載入時記住。

將以下程式碼放在 React 應用中盡可能高的位置。越接近頁面根層級，效果越好！

```js
import { TanStackRouterDevtools } from '@tanstack/react-router-devtools'

function App() {
  return (
    <>
      <Router />
      <TanStackRouterDevtools initialIsOpen={false} />
    </>
  )
}
```

## 固定模式 (Fixed Mode)

若要控制開發者工具 (Devtools) 的位置，請導入 `TanStackRouterDevtoolsPanel`：

```js
import { TanStackRouterDevtoolsPanel } from '@tanstack/react-router-devtools'
```

接著可將其附加到提供的 Shadow DOM 目標：

```js
<TanStackRouterDevtoolsPanel
  shadowDOMTarget={shadowContainer}
  router={router}
```

點擊[這裡](https://tanstack.com/router/latest/docs/framework/react/examples/basic-devtools-panel)查看 StackBlitz 上的實際範例。

### 選項

- `router: Router`
  - 要連接的路由器實例
- `initialIsOpen: Boolean`
  - 設為 `true` 可讓開發者工具 (Devtools) 預設為開啟狀態
- `panelProps: PropsObject`
  - 用於向面板添加屬性。例如，可添加 `className`、`style`（合併並覆蓋預設樣式）等
- `closeButtonProps: PropsObject`
  - 用於向關閉按鈕添加屬性。例如，可添加 `className`、`style`（合併並覆蓋預設樣式）、`onClick`（擴展預設處理程序）等
- `toggleButtonProps: PropsObject`
  - 用於向切換按鈕添加屬性。例如，可添加 `className`、`style`（合併並覆蓋預設樣式）、`onClick`（擴展預設處理程序）等
- `position?: "top-left" | "top-right" | "bottom-left" | "bottom-right"`
  - 預設為 `bottom-left`
  - TanStack Router 標誌的位置，用於開啟和關閉開發者工具面板
- `shadowDOMTarget?: ShadowRoot`
  - 指定開發者工具 (Devtools) 的 Shadow DOM 目標
  - 預設情況下，開發工具樣式會套用至主文件 (light DOM) 的 `<head>` 標籤。當提供 `shadowDOMTarget` 時，樣式將改為在此 Shadow DOM 內部套用

## 嵌入模式 (Embedded Mode)

嵌入模式 (Embedded Mode) 會將開發者工具 (Devtools) 作為常規元件嵌入你的應用中。之後你可以隨心所欲地進行樣式設定！

```js
import { TanStackRouterDevtoolsPanel } from '@tanstack/react-router-devtools'

function App() {
  return (
    <>
      <Router router={router} />
      <TanStackRouterDevtoolsPanel
        router={router}
        style={styles}
        className={className}
      />
    </>
  )
}
```

### 選項

使用這些選項來設定開發者工具 (Devtools) 的樣式。

- `style: StyleObject`
  - 標準的 React 樣式物件，用於以行內樣式設定元件樣式
- `className: string`
  - 標準的 React className 屬性，用於以類別設定元件樣式
