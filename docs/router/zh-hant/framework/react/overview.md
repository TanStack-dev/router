---
source-updated-at: '2025-02-27T22:43:21.000Z'
translation-updated-at: '2025-05-08T20:16:16.714Z'
title: 概述
---

**TanStack Router 是一個用於構建 React 和 Solid 應用程式的路由工具**。其功能包括：

- 100% 推斷的 TypeScript 支援
- 類型安全的導航 (Typesafe navigation)
- 嵌套路由與佈局路由 (Nested Routing and layout routes)（支援無路徑佈局 (pathless layouts)）
- 內建路由載入器與 SWR 快取 (Built-in Route Loaders w/ SWR Caching)
- 專為客戶端資料快取設計 (TanStack Query、SWR 等)
- 自動路由預取 (Automatic route prefetching)
- 非同步路由元件與錯誤邊界 (Asynchronous route elements and error boundaries)
- 基於檔案的路由生成 (File-based Route Generation)
- 類型安全的 JSON 優先搜尋參數狀態管理 API (Typesafe JSON-first Search Params state management APIs)
- 路徑與搜尋參數結構驗證 (Path and Search Parameter Schema Validation)
- 搜尋參數導航 API (Search Param Navigation APIs)
- 自訂搜尋參數解析器/序列化器支援 (Custom Search Param parser/serializer support)
- 搜尋參數中介軟體 (Search param middleware)
- 路由匹配/載入中介軟體 (Route matching/loading middleware)

要快速開始使用，請前往下一頁。若需更詳細的說明，請準備好，我將帶您深入了解！

## 「路由的分岔點」

在技術堆疊中，使用路由工具來構建應用程式被廣泛視為必備選項，通常是您最早需要決策的事項之一。

[//]: # 'WhyChooseTanStackRouter'

**那麼，為什麼要選擇 TanStack Router 而非其他路由工具？**

要回答這個問題，我們需要先審視現有的其他選擇。若仔細尋找，會發現選項眾多，但根據我的經驗，僅有少數值得深入探討：

- **Next.js** — 被廣泛視為啟動新 React 專案的預設框架，其聚焦於效能、工作流程與尖端技術。它的 API 與抽象層雖然強大，但有時會顯得非標準化。其在業界的快速成長與採用導致功能豐富，但也可能讓人感到龐雜甚至臃腫。
- **Remix / React Router** — 這個基於歷史悠久的 React Router 的全端框架，提供了同樣強大的開發者與使用者體驗，其 API 與設計理念緊密遵循網路標準（如 Request/Response），並專注於能在任何 JS 環境中運行。它的許多 API 與抽象層設計精良，甚至啟發了 TanStack Router 的部分功能。然而，其嚴格的設計、附加的類型安全機制，以及有時過度遵循平台 API 的特性，可能讓部分開發者感到不足。

這兩種框架（及其路由工具）都非常優秀，我個人可以證明它們是構建 React 應用程式的絕佳解決方案。但我的經驗也告訴我，這些解決方案仍有改進空間，尤其是在實際提供給開發者的路由 API 方面，若能更進一步，將使應用程式更快、更易用且更令人愉悅。

選擇路由工具的重要性不言而喻，它通常與框架的選擇直接綁定，因為大多數框架都依賴特定的路由工具。

[//]: # 'WhyChooseTanStackRouter'

**這是否意味著 TanStack Router 是一個框架？**

TanStack Router 本身並非傳統意義上的「框架」，因為它並未解決其他常見的全端問題。然而，TanStack Router 的設計允許其升級為全端框架，只需搭配處理打包、部署與伺服器端功能的工具即可。這也是我們正在開發 [TanStack Start](https://tanstack.com/start) 的原因，這是一個基於 TanStack Router 與 Nitro、Vite 等工具的全端框架。

若想深入了解 TanStack Router 的歷史，請參閱 [TanStack Router 的歷史](./decisions-on-dx.md#tanstack-routers-origin-story)。

## 為什麼選擇 TanStack Router？

TanStack Router 滿足了您對其他路由工具的基本期望：

- 嵌套路由、佈局路由、分組路由
- 基於檔案的路由
- 平行資料載入
- 預取
- URL 路徑參數
- 錯誤邊界與處理
- 伺服器渲染 (SSR)
- 路由遮罩

同時，它也提供了一些提升標準的新功能：

- 100% 推斷的 TypeScript 支援
- 類型安全的導航
- 內建 SWR 快取用於載入器
- 專為客戶端資料快取設計 (TanStack Query、SWR 等)
- 類型安全的 JSON 優先搜尋參數狀態管理 API
- 路徑與搜尋參數結構驗證
- 搜尋參數導航 API
- 自訂搜尋參數解析器/序列化器支援
- 搜尋參數中介軟體
- 繼承的路由上下文
- 混合檔案與程式碼路由

讓我們深入探討其中幾個重要功能！

## 100% 推斷的 TypeScript 支援

現今許多工具都標榜「用 TypeScript 編寫」或至少提供覆蓋運行時功能的類型定義，但生態系統中真正以 TypeScript 為核心設計 API 的套件並不多。雖然您可能滿意於路由工具能自動補全選項欄位或捕捉一些屬性/方法拼寫錯誤，但這只是冰山一角。

- TanStack Router 完全知曉您所有路由及其配置，無論在程式碼中的哪個位置。這包括路徑、路徑參數、搜尋參數、上下文以及您提供的任何其他配置。這意味著您可以 100% 類型安全地導航到應用程式中的任何路由，確信您的連結或導航呼叫會成功。
- TanStack Router 提供無損的類型推斷。它使用無數泛型參數來強制傳播您提供的任何類型資訊到其 API 與您的應用程式中。沒有任何其他路由工具能提供這種級別的類型安全與開發者信心。

這對您意味著什麼？

- 透過自動補全與類型提示加速功能開發
- 更安全且快速的程式碼重構
- 確信程式碼將如預期運作

## 一流的搜尋參數

搜尋參數常被視為事後考量，被當作一個可以解析與更新的字串（或字串）黑盒子，僅此而已。現有解決方案也**不**具備類型安全，增加了處理時的謹慎需求。即使是最「現代」的框架與路由工具，也讓您自行決定如何管理這種狀態。有時它們會將搜尋字串解析為物件，有時您得自己用 `URLSearchParams` 處理。

讓我們退一步思考：**搜尋參數是您整個應用程式中最強大的狀態管理工具。** 它們是全域的、可序列化的、可加入書籤的，且可分享的，這使其成為儲存任何需要留存於頁面刷新或社交分享的狀態的完美位置。

為此，搜尋參數在 TanStack Router 中是一等公民。雖然仍基於標準的 URLSearchParams，但 TanStack Router 使用強大的解析器/序列化器來管理更深層與更複雜的資料結構，同時保持類型安全與易用性。

**就像在 URL 中擁有 `useState`！**

搜尋參數具備以下特性：

- 自動以 JSON 解析與序列化
- 驗證與類型化
- 從父路由繼承
- 可在載入器、元件與鉤子中存取
- 透過 useSearch 鉤子、Link、navigate 與 router.navigate API 輕鬆修改
- 可自訂搜尋過濾器與中介軟體
- 透過細粒度的搜尋參數選擇器訂閱以實現高效重新渲染

一旦開始使用 TanStack Router 的搜尋參數，您將難以想像沒有它們的日子。

## 內建快取與友善的資料載入

資料載入是任何應用程式的關鍵部分。雖然大多數現有路由工具提供某種關鍵資料載入 API，但在快取與資料生命週期管理方面往往不足。現有解決方案存在幾個常見問題：

- 完全無快取。資料永遠是最新的，但使用者得反覆等待頻繁存取的資料載入。
- 過度激進的快取。資料快取時間過長，導致資料過時與糟糕的使用者體驗。
- 生硬的失效策略與 API。資料可能過於頻繁失效，導致不必要的網路請求與資源浪費，或者您可能完全無法細粒度控制資料失效時機。

TanStack Router 透過雙管齊下的快取與資料載入方法解決這些問題：

### 內建快取

TanStack Router 提供一個輕量級的內建快取層，與路由工具無縫協作。此快取層大致基於 TanStack Query，但功能較少且 API 表面積更小。與 TanStack Query 類似，合理但強大的預設值確保您的資料被快取以供重用、必要時失效，以及未使用時垃圾回收。它也提供簡單的 API 以手動失效快取。

### 靈活且強大的資料生命週期 API

TanStack Router 設計了一個靈活且強大的資料載入 API，更容易與現有資料獲取庫（如 TanStack Query、SWR、Apollo、Relay 或甚至您的自訂解決方案）整合。可配置的 API 如 `context`、`beforeLoad`、`loaderDeps` 與 `loader` 協同工作，讓您輕鬆定義宣告式資料依賴、預取資料，並輕鬆管理外部資料來源的生命週期。

## 繼承的路由上下文

TanStack Router 的路由器與路由上下文是一個強大功能，允許您定義特定於路由的上下文，並由所有子路由繼承。連路由器與根路由本身也能提供上下文。上下文可以同步或非同步建立，並用於在路由與路由配置間共享資料、設定或甚至函式。這在以下情境尤其有用：

- 認證與授權
- 混合 SSR/CSR 資料獲取與預載
- 主題設定
- 單例與全域工具
- 在預載、載入與渲染階段進行柯里化或部分應用

此外，若路由上下文不具備類型安全，那還有什麼意義？TanStack Router 的路由上下文完全類型安全且零成本推斷。

## 檔案與/或程式碼路由

TanStack Router 同時支援基於檔案與基於程式碼的路由。這種靈活性讓您可以選擇最適合專案需求的方式。

TanStack Router 的檔案路由方法獨特地以使用者為中心。路由配置由 Vite 插件或 TanStack Router CLI 為您生成，而生成程式碼的使用方式完全由您決定！這意味著即使使用檔案路由，您也能完全掌控路由與路由器。

## 致謝

TanStack Router 建立在許多其他開源專案普及的概念與模式上，包括：

- [TRPC](https://trpc.io/)
- [Remix](https://remix.run)
- [Chicane](https://swan-io.github.io/chicane/)
- [Next.js](https://nextjs.org)

我們感謝這些專案開發過程中的投入、風險與研究，並期待將它們設定的標準推向更高。

## 開始吧！

概述已足夠，TanStack Router 還有更多功能等待探索。點擊下一頁按鈕，讓我們開始吧！
