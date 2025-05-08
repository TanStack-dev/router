---
source-updated-at: '2025-01-30T04:47:58.000Z'
translation-updated-at: '2025-05-08T20:14:57.691Z'
id: eslint-plugin-router
title: ESLint 插件路由器
---

TanStack Router 附帶了專屬的 ESLint 外掛 (plugin)。此外掛用於強制執行最佳實踐並幫助您避免常見錯誤。

## 安裝

此外掛是一個獨立套件，需另行安裝：

```sh
npm install -D @tanstack/eslint-plugin-router
```

或

```sh
pnpm add -D @tanstack/eslint-plugin-router
```

或

```sh
yarn add -D @tanstack/eslint-plugin-router
```

或

```sh
bun add -D @tanstack/eslint-plugin-router
```

## 扁平化設定 (`eslint.config.js`)

ESLint 9.0 的發布引入了一種使用扁平化設定格式 (flat config) 來配置 ESLint 的新方式。這種新格式比傳統的 `.eslintrc` 格式更具彈性，能讓您更細緻地配置 ESLint。TanStack Router ESLint 外掛支援此新格式，並提供了一個推薦設定 (recommended config)，可用於啟用外掛的所有推薦規則。

### 推薦的扁平化設定配置

要啟用外掛的所有推薦規則，請添加以下設定：

```js
// eslint.config.js
import pluginRouter from '@tanstack/eslint-plugin-router'

export default [
  ...pluginRouter.configs['flat/recommended'],
  // 其他設定...
]
```

### 自訂扁平化設定配置

或者，您可以載入外掛並僅配置想使用的規則：

```js
// eslint.config.js
import pluginRouter from '@tanstack/eslint-plugin-router'

export default [
  {
    plugins: {
      '@tanstack/router': pluginRouter,
    },
    rules: {
      '@tanstack/router/create-route-property-order': 'error',
    },
  },
  // 其他設定...
]
```

## 傳統設定 (`.eslintrc`)

在 ESLint 9.0 發布之前，最常見的配置方式是使用 `.eslintrc` 檔案。TanStack Router ESLint 外掛仍支援此配置方式。

### 推薦的傳統設定配置

要啟用外掛的所有推薦規則，請在 `extends` 中添加 `plugin:@tanstack/eslint-plugin-router/recommended`：

```json
{
  "extends": ["plugin:@tanstack/eslint-plugin-router/recommended"]
}
```

### 自訂傳統設定配置

或者，在 `plugins` 區塊添加 `@tanstack/eslint-plugin-router`，並配置想使用的規則：

```json
{
  "plugins": ["@tanstack/eslint-plugin-router"],
  "rules": {
    "@tanstack/router/create-route-property-order": "error"
  }
}
```

## 規則

TanStack Router ESLint 外掛提供以下規則：

- [@tanstack/router/create-route-property-order](./create-route-property-order.md)
