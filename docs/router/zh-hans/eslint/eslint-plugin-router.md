---
source-updated-at: 2025-01-30T04:47:58.000Z
translation-updated-at: 2025-04-05T03:38:30.000Z
title: ESLint 插件路由器
id: eslint-plugin-router
---

TanStack Router 提供了专属的 ESLint 插件。该插件用于强制执行最佳实践，并帮助您避免常见错误。

## 安装

该插件是一个独立包，需要单独安装：

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

## 扁平化配置 (`eslint.config.js`)

ESLint 9.0 版本引入了全新的扁平化配置格式。相比传统的 `.eslintrc` 格式，这种新格式更灵活，支持更细粒度的配置。TanStack Router ESLint 插件兼容这一新格式，并提供了推荐配置来启用所有建议规则。

### 推荐的扁平化配置

要启用所有推荐规则，请添加以下配置：

```js
// eslint.config.js
import pluginRouter from '@tanstack/eslint-plugin-router'

export default [
  ...pluginRouter.configs['flat/recommended'],
  // 其他配置...
]
```

### 自定义扁平化配置

您也可以单独加载插件并配置所需规则：

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
  // 其他配置...
]
```

## 传统配置 (`.eslintrc`)

在 ESLint 9.0 之前，最常见的配置方式是通过 `.eslintrc` 文件。TanStack Router ESLint 插件仍支持这种配置方式。

### 推荐的传统配置

要启用所有推荐规则，请在 extends 中添加 `plugin:@tanstack/eslint-plugin-router/recommended`：

```json
{
  "extends": ["plugin:@tanstack/eslint-plugin-router/recommended"]
}
```

### 自定义传统配置

或者在 plugins 中添加 `@tanstack/eslint-plugin-router`，并配置所需规则：

```json
{
  "plugins": ["@tanstack/eslint-plugin-router"],
  "rules": {
    "@tanstack/router/create-route-property-order": "error"
  }
}
```

## 规则列表

TanStack Router ESLint 插件提供以下规则：

- [@tanstack/router/create-route-property-order](./create-route-property-order.md)
