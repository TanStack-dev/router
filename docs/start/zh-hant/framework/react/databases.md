---
source-updated-at: '2025-04-04T19:29:24.000Z'
translation-updated-at: '2025-05-08T20:27:12.609Z'
id: databases
title: 資料庫 (Databases)
---

資料庫 (Databases) 是任何動態應用程式的核心，提供儲存、檢索和管理資料所需的基礎架構。TanStack Start 讓您能輕鬆整合各種資料庫 (Databases)，為應用程式的資料層管理提供靈活的解決方案。

## 我該使用什麼？

TanStack Start **設計上可與任何資料庫供應商 (database provider) 搭配使用**，因此如果您已有偏好的資料庫系統，可以透過 TanStack Start 提供的全端 API (full-stack APIs) 進行整合。無論您使用 SQL、NoSQL 或其他類型的資料庫 (Databases)，TanStack Start 都能滿足您的需求。

## 在 TanStack Start 中使用資料庫 (Databases) 有多簡單？

在 TanStack Start 中使用資料庫 (Databases) 非常簡單，只需從 TanStack Start 的伺服器函式 (server function) 或伺服器路由 (server route) 呼叫您的資料庫適配器/客戶端/驅動程式/服務即可。

以下是一個抽象範例，展示如何連接資料庫 (Databases) 並進行讀寫操作：

```tsx
import { createServerFn } from '@tanstack/react-start'

const db = createMyDatabaseClient()

export const getUser = createServerFn(async ({ ctx }) => {
  const user = await db.getUser(ctx.userId)
  return user
})

export const createUser = createServerFn(async ({ ctx, input }) => {
  const user = await db.createUser(input)
  return user
})
```

這顯然是一個簡化範例，但它證明只要您能從伺服器函式 (server function) 或伺服器路由 (server route) 呼叫，就可以在 TanStack Start 中使用任何資料庫供應商 (database provider)。

## 推薦的資料庫供應商 (Database Providers)

雖然 TanStack Start 設計上可與任何資料庫供應商 (database provider) 搭配使用，但我們強烈建議考慮我們審核過的合作夥伴資料庫供應商 [Neon](https://neon.tech?utm_source=tanstack) 或 [Convex](https://convex.dev?utm_source=tanstack)。這些供應商已通過 TanStack 審核，符合我們的品質、開放性和效能標準，都是滿足您資料庫需求的絕佳選擇。

## 什麼是 Neon？

<a href="https://neon.tech?utm_source=tanstack" alt="Neon Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" width="280">
    <img alt="Neon logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/neon-light.svg" width="280">
  </picture>
</a>

Neon 是一個完全託管的無伺服器 PostgreSQL (serverless PostgreSQL)，提供慷慨的免費方案。它將儲存與運算分離，提供自動擴展 (autoscaling)、分支 (branching) 和無限儲存 (bottomless storage) 功能。使用 Neon，您能獲得 PostgreSQL 的所有強大功能和可靠性，同時兼具現代雲端能力，使其成為 TanStack Start 應用的完美選擇。

Neon 的突出關鍵功能：

- 可自動擴展的無伺服器 PostgreSQL (Serverless PostgreSQL)
- 用於開發和測試的資料庫分支 (Database branching)
- 內建連線池 (Built-in connection pooling)
- 時間點還原 (Point-in-time restore)
- 基於網頁的 SQL 編輯器 (Web-based SQL editor)
- 無限儲存 (Bottomless storage)

- 了解更多 Neon 資訊，請造訪 [Neon 官網](https://neon.tech?utm_source=tanstack)
- 立即註冊，請前往 [Neon 儀表板](https://console.neon.tech/sign_up?utm_source=tanstack)

## 什麼是 Convex？

<a href="https://convex.dev?utm_source=tanstack" alt="Convex Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-white.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" width="280">
    <img alt="Convex logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/convex-color.svg" width="280">
  </picture>
</a>

Convex 是一個強大的無伺服器資料庫平台 (serverless database platform)，簡化了應用程式資料的管理流程。使用 Convex，您無需手動管理資料庫伺服器或編寫複雜查詢，就能建置全端應用程式。Convex 提供即時 (real-time)、可擴展 (scalable) 和具交易性 (transactional) 的資料後端，能與 TanStack Start 無縫整合，是現代網頁應用的絕佳選擇。

Convex 的宣告式資料模型 (declarative data model) 和自動衝突解決機制 (automatic conflict resolution) 確保您的應用程式即使在大規模下也能保持一致性和響應能力。其設計以開發者友善為核心，注重簡潔性和生產力。

- 了解更多 Convex 資訊，請造訪 [Convex 官網](https://convex.dev?utm_source=tanstack)
- 立即註冊，請前往 [Convex 儀表板](https://dashboard.convex.dev/signup?utm_source=tanstack)

## 文件與 API (Documentation & APIs)

關於如何將不同資料庫 (Databases) 與 TanStack Start 整合的文件即將推出！在此期間，請密切關注我們的範例和指南，了解如何充分發揮 TanStack Start 應用程式中資料層的潛力。
