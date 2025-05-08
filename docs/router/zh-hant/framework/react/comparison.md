---
source-updated-at: '2025-04-15T21:31:52.000Z'
translation-updated-at: '2025-05-08T20:16:19.758Z'
title: 比較
toc: false
---

在決定採用新工具之前，了解它與競爭對手的差異總是很有幫助的！

> 本比較表格力求準確且公正。如果您使用過這些函式庫並認為資訊可以改進，歡迎透過頁面底部的「在 GitHub 上編輯此頁面」連結提出修改建議（需附上說明或證據）。

功能/能力對照鍵：

- ✅ 原生支援，開箱即用，無需額外配置或程式碼
- 🔵 需透過附加套件支援
- 🟡 部分支援（以 5 分制標示）
- 🔶 可行，但需自訂程式碼/實作/類型轉換
- 🛑 官方不支援

|                                                                              | TanStack Router / Start                          | React Router DOM [_(官網)_][router]          | Next.JS [_(官網)_][nextjs]                   |
| ---------------------------------------------------------------------------- | ------------------------------------------------ | -------------------------------------------- | -------------------------------------------- |
| GitHub 儲存庫 / 星數                                                         | [![][stars-tanstack-router]][gh-tanstack-router] | [![][stars-router]][gh-router]               | [![][stars-nextjs]][gh-nextjs]               |
| 套件體積                                                                     | [![][bp-tanstack-router]][bpl-tanstack-router]   | [![][bp-router]][bpl-router]                 | ❓                                           |
| 歷史、記憶體與雜湊路由 (History, Memory & Hash Routers)                      | ✅                                               | ✅                                           | 🛑                                           |
| 巢狀/版面路由 (Nested / Layout Routes)                                       | ✅                                               | ✅                                           | 🟡                                           |
| 類似 Suspense 的路由轉場 (Suspense-like Route Transitions)                   | ✅                                               | ✅                                           | ✅                                           |
| 類型安全路由 (Typesafe Routes)                                               | ✅                                               | 🟡 (1/5)                                     | 🟡                                           |
| 基於程式碼的路由 (Code-based Routes)                                         | ✅                                               | ✅                                           | 🛑                                           |
| 基於檔案的路由 (File-based Routes)                                           | ✅                                               | ✅                                           | ✅                                           |
| 虛擬/程式化檔案路由 (Virtual/Programmatic File-based Routes)                 | ✅                                               | ✅                                           | 🛑                                           |
| 路由載入器 (Router Loaders)                                                  | ✅                                               | ✅                                           | ✅                                           |
| SWR 載入器快取 (SWR Loader Caching)                                          | ✅                                               | 🛑                                           | ✅                                           |
| 路由預取 (Route Prefetching)                                                 | ✅                                               | ✅                                           | ✅                                           |
| 自動路由預取 (Auto Route Prefetching)                                        | ✅                                               | ✅                                           | ✅                                           |
| 路由預取延遲 (Route Prefetching Delay)                                       | ✅                                               | 🔶                                           | 🛑                                           |
| 路徑參數 (Path Params)                                                       | ✅                                               | ✅                                           | ✅                                           |
| 類型安全路徑參數 (Typesafe Path Params)                                      | ✅                                               | ✅                                           | 🛑                                           |
| 類型安全路由上下文 (Typesafe Route Context)                                  | ✅                                               | 🛑                                           | 🛑                                           |
| 路徑參數驗證 (Path Param Validation)                                         | ✅                                               | 🛑                                           | 🛑                                           |
| 自訂路徑參數解析/序列化 (Custom Path Param Parsing/Serialization)            | ✅                                               | 🛑                                           | 🛑                                           |
| 排名路由 (Ranked Routes)                                                     | ✅                                               | ✅                                           | ✅                                           |
| 自訂活動連結樣式 (Active Link Customization)                                 | ✅                                               | ✅                                           | ✅                                           |
| 樂觀 UI (Optimistic UI)                                                      | ✅                                               | ✅                                           | 🔶                                           |
| 類型安全絕對+相對導航 (Typesafe Absolute + Relative Navigation)              | ✅                                               | 🛑                                           | 🛑                                           |
| 路由掛載/轉場/卸載事件 (Route Mount/Transition/Unmount Events)               | ✅                                               | 🛑                                           | 🛑                                           |
| 開發者工具 (Devtools)                                                        | ✅                                               | 🛑                                           | 🛑                                           |
| 基本搜尋參數 (Basic Search Params)                                           | ✅                                               | ✅                                           | ✅                                           |
| 搜尋參數鉤子 (Search Param Hooks)                                            | ✅                                               | ✅                                           | ✅                                           |
| `<Link/>`/`useNavigate` 搜尋參數 API                                         | ✅                                               | 🟡 (僅能透過 `to`/`search` 選項使用搜尋字串) | 🟡 (僅能透過 `to`/`search` 選項使用搜尋字串) |
| JSON 搜尋參數 (JSON Search Params)                                           | ✅                                               | 🔶                                           | 🔶                                           |
| 類型安全搜尋參數 (TypeSafe Search Params)                                    | ✅                                               | 🛑                                           | 🛑                                           |
| 搜尋參數結構驗證 (Search Param Schema Validation)                            | ✅                                               | 🛑                                           | 🛑                                           |
| 搜尋參數不可變性 + 結構共享 (Search Param Immutability + Structural Sharing) | ✅                                               | 🔶                                           | 🛑                                           |
| 自訂搜尋參數解析/序列化 (Custom Search Param parsing/serialization)          | ✅                                               | 🔶                                           | 🛑                                           |
| 搜尋參數中介軟體 (Search Param Middleware)                                   | ✅                                               | 🛑                                           | 🛑                                           |
| Suspense 路由元素 (Suspense Route Elements)                                  | ✅                                               | ✅                                           | ✅                                           |
| 路由錯誤元素 (Route Error Elements)                                          | ✅                                               | ✅                                           | ✅                                           |
| 路由待處理元素 (Route Pending Elements)                                      | ✅                                               | ✅                                           | ✅                                           |
| `<Block>`/`useBlocker`                                                       | ✅                                               | 🔶                                           | ❓                                           |
| 延遲基礎元件 (Deferred Primitives)                                           | ✅                                               | ✅                                           | ✅                                           |
| 導航滾動恢復 (Navigation Scroll Restoration)                                 | ✅                                               | ✅                                           | ❓                                           |
| 元素滾動恢復 (ElementScroll Restoration)                                     | ✅                                               | 🛑                                           | 🛑                                           |
| 非同步滾動恢復 (Async Scroll Restoration)                                    | ✅                                               | 🛑                                           | 🛑                                           |
| 路由失效 (Router Invalidation)                                               | ✅                                               | ✅                                           | ✅                                           |
| 執行時路由操作 (Runtime Route Manipulation)                                  | 🛑                                               | ✅                                           | ✅                                           |
| 平行路由 (Parallel Routes)                                                   | 🛑                                               | 🛑                                           | ✅                                           |
| --                                                                           | --                                               | --                                           | --                                           |
| **全端功能**                                                                 | --                                               | --                                           | --                                           |
| 伺服器渲染 (SSR)                                                             | ✅                                               | ✅                                           | ✅                                           |
| 串流式 SSR (Streaming SSR)                                                   | ✅                                               | ✅                                           | ✅                                           |
| 通用 RPC (Generic RPCs)                                                      | ✅                                               | 🛑                                           | 🛑                                           |
| 通用 RPC 中介軟體 (Generic RPC Middleware)                                   | ✅                                               | 🛑                                           | 🛑                                           |
| React 伺服器函式 (React Server Functions)                                    | ✅                                               | 🛑                                           | ✅                                           |
| React 伺服器函式中介軟體 (React Server Function Middleware)                  | ✅                                               | 🛑                                           | 🛑                                           |
| API 路由 (API Routes)                                                        | ✅                                               | ✅                                           | ✅                                           |
| API 中介軟體 (API Middleware)                                                | ✅                                               | 🛑                                           | ✅                                           |
| React 伺服器元件 (React Server Components)                                   | 🛑                                               | 🛑                                           | ✅                                           |
| `<Form>` API                                                                 | 🛑                                               | ✅                                           | ✅                                           |

[bp-tanstack-router]: https://badgen.net/bundlephobia/minzip/@tanstack/react-router
[bpl-tanstack-router]: https://bundlephobia.com/result?p=@tanstack/react-router
[gh-tanstack-router]: https://github.com/tanstack/router
[stars-tanstack-router]: https://img.shields.io/github/stars/tanstack/router?label=%F0%9F%8C%9F
[_]: _
[router]: https://github.com/remix-run/react-router
[bp-router]: https://badgen.net/bundlephobia/minzip/react-router
[gh-router]: https://github.com/remix-run/react-router
[stars-router]: https://img.shields.io/github/stars/remix-run/react-router?label=%F0%9F%8C%9F
[bpl-router]: https://bundlephobia.com/result?p=react-router
[bpl-history]: https://bundlephobia.com/result?p=history
[_]: _
[nextjs]: https://nextjs.org/docs/routing/introduction
[bp-nextjs]: https://badgen.net/bundlephobia/minzip/next.js?label=All
[gh-nextjs]: https://github.com/vercel/next.js
[stars-nextjs]: https://img.shields.io/github/stars/vercel/next.js?label=%F0%9F%8C%9F
[bpl-nextjs]: https://bundlephobia.com/result?p=next
