---
source-updated-at: '2025-02-22T02:52:50.000Z'
translation-updated-at: '2025-05-08T20:14:49.054Z'
title: 安裝
---

# 安裝 (Installation)

你可以使用任何 [NPM](https://npmjs.com) 套件管理工具來安裝 TanStack Router。

```sh
npm install @tanstack/react-router
# 或
pnpm add @tanstack/react-router
# 或
yarn add @tanstack/react-router
# 或
bun add @tanstack/react-router
# 或
deno add npm:@tanstack/react-router
```

TanStack Router 目前僅相容於 React (搭配 ReactDOM) 和 Solid。如果你想為 React Native、Angular 或 Vue 的轉接器 (adapter) 貢獻程式碼，請透過 [Discord](https://tlinz.com/discord) 聯繫我們。

### 需求 (Requirements)

[//]: # 'Requirements'

- `react` 版本需為 v18.x.x 或 v19.x.x
- `react-dom` 版本需為 v18.x.x 或 v19.x.x
  - 請注意必須使用 `ReactDOM.createRoot`
  - 不支援舊版的 `.render()` 函式

[//]: # 'Requirements'

TypeScript 是*可選*的，但**強烈**建議使用！如果你正在使用 TypeScript，請確保你的版本是 `typescript>=v5.3.x`。

> [!IMPORTANT]
> 我們的目標是支援 TypeScript 最新的五個次要版本。如果你使用的是舊版，可能會遇到問題。請升級至最新版本的 TypeScript 以確保相容性。我們可能會在次要或修補版本更新中，無預警地停止支援上述範圍之外的舊版 TypeScript。
