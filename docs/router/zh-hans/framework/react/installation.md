---
source-updated-at: 2025-02-22T02:52:50.000Z
translation-updated-at: 2025-04-05T04:23:46.151Z
title: 安装
---

您可以使用任何 [NPM](https://npmjs.com) 包管理器来安装 TanStack Router。

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

TanStack Router 目前仅兼容 React（配合 ReactDOM）和 Solid。如果您希望为 React Native、Angular 或 Vue 的适配器贡献力量，请通过 [Discord](https://tlinz.com/discord) 联系我们。

### 要求

[//]: # 'Requirements'

- `react` 版本需为 v18.x.x 或 v19.x.x
- `react-dom` 版本需为 v18.x.x 或 v19.x.x
  - 注意：必须使用 `ReactDOM.createRoot`。
  - 不支持旧版的 `.render()` 函数。

[//]: # 'Requirements'

TypeScript 是*可选*的，但**强烈**推荐使用！如果您正在使用 TypeScript，请确保其版本为 `typescript>=v5.3.x`。

> [!IMPORTANT]
> 我们的目标是支持 TypeScript 最新的五个次要版本。如果您使用的是较旧版本，可能会遇到问题。请升级至 TypeScript 的最新版本以确保兼容性。对于超出上述范围的旧版 TypeScript，我们可能会在次要或补丁版本中无预警地停止支持。
