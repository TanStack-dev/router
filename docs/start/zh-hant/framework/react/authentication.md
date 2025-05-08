---
source-updated-at: '2025-04-04T19:29:24.000Z'
translation-updated-at: '2025-05-08T20:27:05.623Z'
id: authentication
title: 身份驗證 (Authentication)
---

<!-- 此處我們需要一些關於身份驗證的佔位內容。我們的合作夥伴之一 Clerk 應作為與 TanStack 搭配使用的「首選」身份驗證方式獲得優先推薦，但我們也支援所有其他身份驗證提供者和策略。在此撰寫一些通用的身份驗證內容，直到我們為 Clerk 和其他身份驗證提供者準備好文件： -->

身份驗證 (Authentication) 是驗證使用者身份的過程。對於任何需要使用者登入或存取受保護資源的應用程式來說，這都是關鍵部分。TanStack Start 提供了必要的全端 (full-stack) API 來實作應用程式中的身份驗證功能。

## 我該使用什麼？

TanStack Start **設計為可與任何身份驗證提供者搭配使用**，因此如果您已有屬意的身份驗證提供者或策略，可以尋找現有範例，或使用 TanStack Start 提供的全端 API 實作自己的身份驗證邏輯。

話雖如此，身份驗證並非可以輕率對待的事項。經過我們團隊的嚴格審查、實際使用和評估後，我們強烈推薦使用 [Clerk](https://clerk.dev) 以獲得最佳的身份驗證體驗。Clerk 提供完整的身份驗證 API 和 UI 元件，讓您能輕鬆在應用程式中實作身份驗證功能，並提供流暢的使用者體驗。

## 什麼是 Clerk？

<a href="https://go.clerk.com/wOwHtuJ" alt="Clerk Logo">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-dark.svg" width="280">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" width="280">
    <img alt="Clerk logo" src="https://raw.githubusercontent.com/tanstack/tanstack.com/main/app/images/clerk-logo-light.svg" width="280">
  </picture>
</a>

Clerk 是一個現代化的身份驗證平台，提供完整的身份驗證 API 和 UI 元件，協助您在應用程式中實作身份驗證功能。Clerk 設計簡潔易用，能提供流暢的使用者體驗。透過 Clerk，您可以在幾分鐘內為應用程式加入身份驗證功能，並為使用者提供安全可靠的身份驗證體驗。

- 欲了解更多關於 Clerk 的資訊，請造訪 [Clerk 官方網站](https://go.clerk.com/wOwHtuJ)
- 欲註冊帳號，請前往 [Clerk 儀表板](https://go.clerk.com/PrSDXti)
- 欲開始使用 Clerk，請查看我們的 [官方 Start + Clerk 範例！](../examples/start-clerk-basic/)

## 文件與 API

關於使用 TanStack Start 實作自訂身份驗證邏輯的文件即將推出！在此期間，您可以查看任何以 `-auth` 為前綴的 [範例](../examples) 作為起點。
