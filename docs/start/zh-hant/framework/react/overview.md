---
source-updated-at: '2025-05-05T22:15:09.000Z'
translation-updated-at: '2025-05-08T20:26:42.991Z'
id: overview
title: 總覽
---

TanStack Start 是一個基於 TanStack Router 的全端 React 框架。它使用 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 等工具，提供完整的伺服器渲染 (SSR)、串流、伺服器函式、打包等功能，並可立即部署至您喜愛的託管服務供應商！

## 該選擇 Router 還是 Start？

TanStack Router 是一個強大、類型安全且功能豐富的 React 應用程式路由系統。它能輕鬆應對最複雜的全端路由需求。TanStack Start 在 Router 的類型系統基礎上，進一步提供類型安全的全端 API，讓您保持高效開發。

TanStack Router 提供以下功能：

- 100% 推論的 TypeScript 支援
- 類型安全的導航
- 嵌套路由與無路徑的版面配置路由
- 內建路由載入器與 SWR 快取
- 專為客戶端資料快取設計（TanStack Query、SWR 等）
- 自動路由預取
- 非同步路由元件與錯誤邊界
- 基於檔案的路由生成
- 類型安全的 JSON 優先搜尋參數狀態管理 API
- 路徑與搜尋參數的結構驗證
- 搜尋參數導航 API
- 自訂搜尋參數解析器/序列化器支援
- 搜尋參數中介軟體
- 路由匹配/載入中介軟體

TanStack Start 提供以下功能：

- 完整文件的伺服器渲染 (SSR)
- 串流
- 伺服器函式 / RPC
- 打包
- 部署
- 全端類型安全

**總結來說，客戶端路由請使用 TanStack Router，全端路由則選擇 TanStack Start。**

## 運作原理

TanStack Start 使用 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 來打包並部署您的應用程式。事實上，這些正是驅動 Solid Start 的相同工具！透過這些工具，我們能實現以往無法做到的事：

- 提供統一的 API 用於伺服器渲染 (SSR)、串流與水合 (hydration)
- 從客戶端程式碼中分離出僅限伺服器的程式碼（例如伺服器函式）
- 為應用程式打包以便部署至任何託管服務供應商

## 適用情境

若您想建構具備以下需求的全端 React 應用程式，TanStack Start 將是完美選擇：

- 完整文件的伺服器渲染 (SSR) 與水合 (hydration)
- 串流
- 伺服器函式 / RPC
- 全端類型安全
- 強健的路由系統
- 豐富的客戶端互動性

## 不適用情境

以下情況可能不適合使用 TanStack Start：

- 您的網站將是 100% 靜態內容
- 您的目標是零 JavaScript 或僅需最低限度客戶端互動性的伺服器渲染網站
- 您正在尋找以 React 伺服器元件 (RSC) 為優先的框架（我們將很快以獨特方式支援 RSC！）

## TanStack Start 的資金來源

TanStack 與合作夥伴密切合作，不僅提供最佳的開發者體驗，也推出經過業界專家驗證、能隨處運作的解決方案。每位合作夥伴在 TanStack 生態系中都扮演獨特角色：

- **Clerk**  
  <a href="https://go.clerk.com/wOwHtuJ" alt="Clerk Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" style="height: 40px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">  
  <img alt="Clerk logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">  
  </picture>  
  </a>  
  為現代網頁應用程式（包括 TanStack Start 應用程式）提供最佳驗證體驗。Clerk 為 TanStack Start 用戶提供一流的整合與驗證解決方案，並與 TanStack 團隊緊密合作，確保 TanStack Start 的 API 符合最新的驗證最佳實踐。

- **Netlify**  
  <a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" style="height: 90px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">  
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">  
  </picture>  
  </a>  
  領先的網頁應用程式託管平台，提供快速、安全且可靠的部署環境。我們與 Netlify 密切合作，確保 TanStack Start 應用程式不僅能無縫部署至其平台，更能實現效能、安全與可靠性的最佳實踐，無論您最終選擇部署至何處。

- **Neon**  
  <a href="https://neon.tech?utm_source=tanstack" alt="Neon Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-dark.svg" style="height: 60px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" style="height: 60px;">  
  <img alt="Neon logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" style="height: 60px;">  
  </picture>  
  </a>  
  專為現代全端應用程式打造的無伺服器、自動擴展 Postgres 解決方案。Neon 提供與 TanStack Start 的豐富整合機會，包括伺服器函式與資料庫支援的路由。我們共同簡化開發者使用 TanStack 時的資料庫體驗。

- **Convex**  
  <a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" style="height: 40px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">  
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">  
  </picture>  
  </a>  
  與 TanStack Start 無縫整合的無伺服器資料庫平台。Convex 旨在簡化應用程式資料管理流程，提供即時、可擴展且具交易性的資料後端，完美搭配 TanStack Start 應用程式。Convex 也與 TanStack 團隊緊密合作，確保 TanStack Start 的 API 符合最新的資料庫最佳實踐。

- **Sentry**  
  <a href="https://sentry.io?utm_source=tanstack" alt='Sentry Logo'>  
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">  
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-dark.svg" style="height: 60px;">  
  <img alt="Sentry logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">  
  </picture>  
  </a>  
  與 TanStack Start 無縫整合的強大、功能完整的可觀測性平台。Sentry 協助開發者即時監控並修復當機問題，提供應用程式效能與錯誤追蹤的深入洞察。Sentry 與 TanStack 團隊緊密合作，確保 TanStack Start 的 API 符合最新的可觀測性最佳實踐。

## 準備開始了嗎？

前往下一頁，了解如何安裝 TanStack Start 並建立您的第一個應用程式！
