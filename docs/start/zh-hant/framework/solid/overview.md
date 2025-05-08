---
source-updated-at: '2025-03-27T02:34:58.000Z'
translation-updated-at: '2025-05-08T20:26:10.961Z'
id: overview
title: 總覽
---

TanStack Start (**Solid 實驗性版本**) 是一個基於 TanStack Router 的全端框架。它透過 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 等工具提供完整的文件伺服器渲染 (SSR)、串流、伺服器函式、打包等功能。它已準備好部署到您喜愛的託管服務提供商！

## Router 還是 Start？

TanStack Router 是一個強大、類型安全且功能豐富的路由系統，專為 Solid 應用程式設計。它能輕鬆應對最複雜的全端路由需求。TanStack Start 建立在 Router 的類型系統之上，提供類型安全的全端 API，讓您保持高效開發。

使用 TanStack Router 您將獲得：

- 100% 推斷的 TypeScript 支援
- 類型安全的導航
- 嵌套路由與無路徑的版面路由
- 內建路由載入器與 SWR 快取
- 專為客戶端資料快取設計 (TanStack Query、SWR 等)
- 自動路由預取
- 非同步路由元件與錯誤邊界
- 基於檔案的路由生成
- 類型安全的 JSON-first 搜尋參數狀態管理 API
- 路徑與搜尋參數的結構驗證
- 搜尋參數導航 API
- 自訂搜尋參數解析器/序列化器支援
- 搜尋參數中介軟體
- 路由匹配/載入中介軟體

使用 TanStack Start 您將獲得：

- 完整的文件伺服器渲染 (SSR)
- 串流
- 伺服器函式 / RPCs
- 打包
- 部署
- 全端類型安全

**總結來說，使用 TanStack Router 處理客戶端路由，使用 TanStack Start 處理全端路由。**

## 它是如何運作的？

TanStack Start 使用 [Nitro](https://nitro.unjs.io/) 和 [Vite](https://vitejs.dev/) 來打包並部署您的應用程式。事實上，這些正是驅動 Solid Start 的相同工具！有了這些工具，我們可以實現一些以往無法做到的事情：

- 提供統一的 API 用於伺服器渲染 (SSR)、串流與水合 (hydration)
- 從客戶端程式碼中提取僅限伺服器的程式碼 (例如伺服器函式)
- 為應用程式打包以便部署到任何託管服務提供商

## 何時該使用它？

如果您想建立一個具有以下需求的全端 Solid 應用程式，TanStack Start 將是完美選擇：

- 完整的文件伺服器渲染 (SSR) 與水合 (hydration)
- 串流
- 伺服器函式 / RPCs
- 全端類型安全
- 強健的路由系統
- 豐富的客戶端互動性

## 何時可能不適合使用它？

以下情況 TanStack Start 可能不適合您：

- 您的網站將是 100% 靜態的
- 您的目標是零 JS 或僅有最低限度客戶端互動性的伺服器渲染網站

## TanStack Start 如何獲得資金支持？

TanStack 與我們的合作夥伴密切合作，提供最佳的開發者體驗，同時也提供可在任何地方運作且經過業界專家驗證的解決方案。每位合作夥伴在 TanStack 生態系統中都扮演著獨特的角色：

- **Clerk**
  <a href="https://go.clerk.com/wOwHtuJ" alt="Clerk Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" style="height: 40px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">
  <img alt="Clerk logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" style="height: 40px;">
  </picture>
  </a>
  為現代網頁應用程式 (包括 TanStack Start 應用程式) 提供最佳的驗證體驗。Clerk 為 TanStack Start 用戶提供一流的整合與驗證解決方案，並與 TanStack 團隊緊密合作，確保 TanStack Start 提供符合最新驗證最佳實踐的 API。
- **Netlify**
  <a href="https://www.netlify.com?utm_source=tanstack" alt="Netlify Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-dark.svg" style="height: 90px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">
    <img alt="Netlify logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/netlify-light.svg" style="height: 90px;">
  </picture>
  </a>
  領先的網頁應用程式託管平台，提供快速、安全且可靠的環境來部署您的網頁應用程式。我們與 Netlify 密切合作，確保 TanStack Start 應用程式不僅能無縫部署到他們的平台，還能實現性能、安全性和可靠性的最佳實踐，無論您最終部署到哪裡。
- **Neon**
  <a href="https://neon.tech?utm_source=tanstack" alt="Neon Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-dark.svg" style="height: 60px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" style="height: 60px;">
  <img alt="Neon logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" style="height: 60px;">
  </picture>
  </a>
  專為現代全端應用程式設計的無伺服器、自動擴展的 Postgres 解決方案。Neon 提供與 TanStack Start 的豐富整合機會，包括伺服器函式和基於資料庫的路由。我們共同簡化使用 TanStack 的開發者的資料庫體驗。
- **Convex**
  <a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" style="height: 40px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">
  <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" style="height: 40px;">
  </picture>
  </a>
  一個與 TanStack Start 無縫整合的無伺服器資料庫平台。Convex 旨在簡化管理應用程式資料的流程，並提供與 TanStack Start 應用程式良好配合的即時、可擴展且具事務性的資料後端。Convex 還與 TanStack 團隊密切合作，確保 TanStack Start 提供符合最新資料庫最佳實踐的 API。
- **Sentry**
  <a href="https://sentry.io?utm_source=tanstack" alt='Sentry Logo'>
  <picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">
  <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-dark.svg" style="height: 60px;">
  <img alt="Sentry logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/sentry-wordmark-light.svg" style="height: 60px;">
  </picture>
  </a>
  一個功能強大且全面的可觀測性平台，與 TanStack Start 無縫整合。Sentry 幫助開發者即時監控並修復崩潰問題，並提供應用程式效能與錯誤追蹤的深入分析。Sentry 與 TanStack 團隊緊密合作，確保 TanStack Start 提供符合最新可觀測性最佳實踐的 API。

## 準備好開始了嗎？

前往下一頁了解如何安裝 TanStack Start 並建立您的第一個應用程式！
