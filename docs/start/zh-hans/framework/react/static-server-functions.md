---
source-updated-at: 2025-02-25T21:17:33.000Z
translation-updated-at: 2025-04-05T03:40:55.000Z
title: 静态服务端函数
id: static-server-functions
---

## 什么是静态服务端函数？

静态服务端函数是在构建时执行，并在使用预渲染/静态生成时缓存为静态资源的服务端函数。通过向`createServerFn`传递`type: 'static'`选项，可以将其设置为"静态"模式：

```tsx
const myServerFn = createServerFn({ type: 'static' }).handler(async () => {
  return 'Hello, world!'
})
```

该模式的运行流程如下：

- 构建阶段
  - 在构建时预渲染过程中，会执行带有`type: 'static'`的服务端函数
  - 结果会被缓存到构建输出目录中，作为一个静态JSON文件，存储路径由函数ID和参数/载荷哈希值派生
  - 结果在预渲染/静态生成过程中正常返回，并用于预渲染页面
- 运行阶段
  - 初始时，返回预渲染页面的HTML，服务端函数数据被嵌入到HTML中
  - 当客户端挂载时，嵌入的服务端函数数据会被水合激活
  - 对于后续的客户端调用，服务端函数会被替换为向静态JSON文件发起fetch请求

## 自定义服务端函数静态缓存

默认情况下，静态服务端函数缓存实现通过node的`fs`模块在构建输出目录中存储和检索静态数据，在运行时同样使用`fetch`请求相同的静态文件来获取数据。

可以通过导入并调用`createServerFnStaticCache`函数创建自定义缓存实现，然后调用`setServerFnStaticCache`进行设置，来自定义该接口：

```tsx
import {
  createServerFnStaticCache,
  setServerFnStaticCache,
} from '@tanstack/react-start/client'

const myCustomStaticCache = createServerFnStaticCache({
  setItem: async (ctx, data) => {
    // 将静态数据存储到自定义缓存中
  },
  getItem: async (ctx) => {
    // 从自定义缓存中检索静态数据
  },
  fetchItem: async (ctx) => {
    // 在运行时从自定义缓存中获取静态数据
  },
})

setServerFnStaticCache(myCustomStaticCache)
```
