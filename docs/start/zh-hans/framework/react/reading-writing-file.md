---
source-updated-at: '2025-05-05T14:23:49.000Z'
translation-updated-at: '2025-05-06T22:21:13.236Z'
id: reading-and-writing-file
title: Building a Full Stack DevJokes App with TanStack Start
---

本教程将指导您使用 TanStack Start 构建一个完整的全栈应用。您将创建一个 DevJokes 应用，用户可以查看和添加开发者主题的笑话，展示 TanStack Start 的核心概念，包括服务端函数 (server functions)、基于文件的数据存储和 React 组件。

以下是应用的实际效果演示：

<iframe width="560" height="315" src="https://www.youtube.com/embed/zd0rtKbtlgU?si=7W1Peoo0W0WvZmAd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

完整代码已发布在 [GitHub](https://github.com/shrutikapoor08/devjokes)。

## 您将学习到

1. 搭建 TanStack Start 项目
2. 实现服务端函数 (server functions)
3. 读写文件数据
4. 使用 React 组件构建完整 UI
5. 使用 TanStack Router 进行数据获取和导航

## 先决条件

- 基础的 React 和 TypeScript 知识
- 本地已安装 Node.js 和 `pnpm`

## 扩展知识

- [服务端渲染 (SSR)](https://tanstack.com/router/latest/docs/framework/react/guide/ssr)
- [TanStack Router 核心概念](https://tanstack.com/router/latest/docs/framework/react/routing/routing-concepts)
- [React Query 概念](https://tanstack.com/query/latest/docs/framework/react/overview)

## 搭建 TanStack Start 项目

首先创建一个新项目：

```bash
pnpx create-start-app devjokes
cd devjokes
```

运行脚本时会询问几个配置问题，您可以选择适合的选项或直接按回车使用默认值。

可选地，您可以通过 `--add-on` 标志添加扩展功能，如 Shadcn、Clerk、Convex、TanStack Query 等。

完成设置后，安装依赖并启动开发服务器：

```bash
pnpm i
pnpm dev
```

本项目还需要额外安装几个包：

```bash
# 安装 uuid 用于生成唯一 ID
pnpm add uuid
pnpm add -D @types/uuid
```

## 项目结构解析

此时项目结构应如下所示：

```
/devjokes
├── src/
│   ├── routes/
│   │   ├── __root.tsx                    # 根布局
│   │   ├── index.tsx                     # 首页
│   │   ├── demo.start.server-funcs.tsx   # 示例服务端函数
│   │   └── demo.start.api-request.tsx    # 示例 API 请求
│   ├── api/                              # API 端点
│   ├── components/                       # React 组件
│   ├── api.ts                            # API 处理器
│   ├── client.tsx                        # 客户端入口
│   ├── router.tsx                        # 路由配置
│   ├── routeTree.gen.ts                  # 生成的路由树
│   ├── ssr.tsx                           # 服务端渲染
│   └── styles.css                        # 全局样式
├── public/                               # 静态资源
├── app.config.ts                         # TanStack Start 配置
├── package.json                          # 项目依赖
└── tsconfig.json                         # TypeScript 配置
```

初次接触可能觉得复杂，以下是需要关注的核心文件：

1. `router.tsx` - 配置应用路由
2. `src/routes/__root.tsx` - 根布局组件，可添加全局样式和组件
3. `src/routes/index.tsx` - 首页
4. `client.tsx` - 客户端入口
5. `ssr.tsx` - 处理服务端渲染

项目启动后，您可以在 `localhost:3000` 访问应用，将看到 TanStack Start 的默认欢迎页：

![TanStack Start 初始化欢迎页](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746312482/Photo-1_lpechn.png)

## 第一步：从文件读取数据

首先为笑话创建基于文件的存储系统。

### 步骤 1.1：创建包含笑话的 JSON 文件

在项目根目录创建 `data` 文件夹和 `jokes.json` 文件：

```bash
mkdir -p src/data
touch src/data/jokes.json
```

添加示例笑话数据：

```json
[
  {
    "id": "1",
    "question": "Why don't keyboards sleep?",
    "answer": "Because they have two shifts"
  },
  {
    "id": "2",
    "question": "Are you a RESTful API?",
    "answer": "Because you GET my attention, PUT some love, POST the cutest smile, and DELETE my bad day"
  },
  {
    "id": "3",
    "question": "I used to know a joke about Java",
    "answer": "But I ran out of memory."
  },
  {
    "id": "4",
    "question": "Why do Front-End Developers eat lunch alone?",
    "answer": "Because, they don't know how to join tables."
  },
  {
    "id": "5",
    "question": "I am declaring a war.",
    "answer": "var war;"
  }
]
```

### 步骤 1.2：创建数据类型定义

在 `src/types/index.ts` 创建类型定义文件：

```typescript
// src/types/index.ts
export interface Joke {
  id: string
  question: string
  answer: string
}

export type JokesData = Joke[]
```

### 步骤 1.3：创建读取文件的服务器函数

创建 `src/serverActions/jokesActions.ts` 文件实现读写操作，使用 [`createServerFn`](https://tanstack.com/start/latest/docs/framework/react/server-functions)：

```tsx
// src/serverActions/jokesActions.ts

import { createServerFn } from '@tanstack/react-start'
import * as fs from 'node:fs'
import type { JokesData } from '../types'

const JOKES_FILE = 'src/data/jokes.json'

export const getJokes = createServerFn({ method: 'GET' }).handler(async () => {
  const jokes = await fs.promises.readFile(JOKES_FILE, 'utf-8')
  return JSON.parse(jokes) as JokesData
})
```

这段代码使用 `createServerFn` 创建服务端函数读取 JSON 文件，`handler` 函数中使用 `fs` 模块读取文件。

### 步骤 1.4：在客户端调用服务端函数

通过 TanStack Router（已内置在 TanStack Start 中）调用服务端函数。

创建 `JokesList` 组件展示笑话列表：

```tsx
// src/components/JokesList.tsx
import { Joke } from '../types'

interface JokesListProps {
  jokes: Joke[]
}

export function JokesList({ jokes }: JokesListProps) {
  if (!jokes || jokes.length === 0) {
    return <p className="text-gray-500 italic">No jokes found. Add some!</p>
  }

  return (
    <div className="space-y-4">
      <h2 className="text-xl font-semibold">Jokes Collection</h2>
      {jokes.map((joke) => (
        <div
          key={joke.id}
          className="bg-white p-4 rounded-lg shadow-md border border-gray-200"
        >
          <p className="font-bold text-lg mb-2">{joke.question}</p>
          <p className="text-gray-700">{joke.answer}</p>
        </div>
      ))}
    </div>
  )
}
```

在 `App.jsx` 中调用服务端函数：

```jsx
// App.jsx
import { createFileRoute } from '@tanstack/react-router'
import { getJokes } from './serverActions/jokesActions'
import { JokesList } from './JokesList'

export const Route = createFileRoute('/')({
  loader: async () => {
    // 路由访问时加载笑话数据
    return getJokes()
  },
  component: App,
})

const App = () => {
  const jokes = Route.useLoaderData() || []

  return (
    <div className="p-4 flex flex-col">
      <h1 className="text-2xl">DevJokes</h1>
      <JokesList jokes={jokes} />
    </div>
  )
}
```

页面加载时，`jokes` 将自动包含来自 `jokes.json` 的数据！

添加 Tailwind 样式后，应用效果如下：

![包含 5 个笑话的 DevJoke 应用](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746313558/Screenshot_2025-05-03_at_4.04.50_PM_w0eern.png)

## 第二步：向文件写入数据

目前我们已能成功读取文件！现在用相同方法通过 `createServerFunction` 写入 `jokes.json` 文件。

### 步骤 2.1：创建写入文件的服务器函数

修改 `jokes.json` 文件以添加新笑话。创建另一个使用 `POST` 方法的服务端函数：

```tsx
// src/serverActions/jokesActions.ts
import { createServerFn } from '@tanstack/react-start'
import * as fs from 'node:fs'
import { v4 as uuidv4 } from 'uuid' // 添加此导入
import type { Joke, JokesData } from '../types'

export const addJoke = createServerFn({ method: 'POST' })
  .validator((data: { question: string; answer: string }) => {
    // 验证输入数据
    if (!data.question || !data.question.trim()) {
      throw new Error('Joke question is required')
    }
    if (!data.answer || !data.answer.trim()) {
      throw new Error('Joke answer is required')
    }
    return data
  })
  .handler(async ({ data }) => {
    try {
      // 从文件读取现有笑话
      const jokesData = await getJokes()

      // 创建带唯一 ID 的新笑话
      const newJoke: Joke = {
        id: uuidv4(),
        question: data.question,
        answer: data.answer,
      }

      // 将新笑话添加到列表
      const updatedJokes = [...jokesData, newJoke]

      // 将更新后的笑话写回文件
      await fs.promises.writeFile(
        JOKES_FILE,
        JSON.stringify(updatedJokes, null, 2),
        'utf-8',
      )

      return newJoke
    } catch (error) {
      console.error('Failed to add joke:', error)
      throw new Error('Failed to add joke')
    }
  })
```

代码解析：

- 使用 `createServerFn` 创建可在客户端调用的服务端函数
- 通过 `validator` 验证输入数据
- `handler` 函数执行实际写入操作
- `getJokes` 读取现有笑话
- `addJoke` 验证并添加新笑话
- 使用 `uuidv4()` 生成笑话唯一 ID

### 步骤 2.2：添加表单以新增笑话

创建 `JokeForm.jsx` 组件：

```tsx
// src/components/JokeForm.tsx
import { useState } from 'react'
import { useRouter } from '@tanstack/react-router'
import { addJoke } from '../serverActions/jokesActions'

export function JokeForm() {
  const router = useRouter()
  const [question, setQuestion] = useState('')
  const [answer, setAnswer] = useState('')
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [error, setError] = useState<string | null>(null)

  return (
    <form onSubmit={handleSubmit} className="flex flex-row gap-2 mb-6">
      {error && (
        <div className="bg-red-100 text-red-700 p-2 rounded mb-4">{error}</div>
      )}

      <div className="flex flex-col sm:flex-row gap-4 mb-8">
        <input
          id="question"
          type="text"
          placeholder="输入笑话问题"
          className="w-full p-2 border rounded focus:ring focus:ring-blue-300 flex-1"
          value={question}
          onChange={(e) => setQuestion(e.target.value)}
          required
        />

        <input
          id="answer"
          type="text"
          placeholder="输入笑话答案"
          className="w-full p-2 border rounded focus:ring focus:ring-blue-300 flex-1 py-4"
          value={answer}
          onChange={(e) => setAnswer(e.target.value)}
          required
        />

        <button
          type="submit"
          disabled={isSubmitting}
          className="bg-blue-500 hover:bg-blue-600 text-white font-medium rounded disabled:opacity-50 px-4"
        >
          {isSubmitting ? '添加中...' : '添加笑话'}
        </button>
      </div>
    </form>
  )
}
```

### 步骤 2.3：将表单与服务端函数连接

在 `handleSubmit` 函数中调用 `addJoke` 服务端函数：

```tsx
//JokeForm.tsx
import { useState } from 'react'
import { addJoke } from '../serverActions/jokesActions'
import { useRouter } from '@tanstack/react-router'

export function JokeForm() {
  const router = useRouter()
  const [question, setQuestion] = useState('')
  const [answer, setAnswer] = useState('')
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [error, setError] = useState<string | null>(null)

  const handleSubmit = async () => {
    if (!question || !answer || isSubmitting) return
    try {
      setIsSubmitting(true)
      await addJoke({
        data: { question, answer },
      })

      // 清空表单
      setQuestion('')
      setAnswer('')

      // 刷新数据
      router.invalidate()
    } catch (error) {
      console.error('Failed to add joke:', error)
      setError('添加笑话失败')
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <form onSubmit={handleSubmit} className="flex flex-row gap-2 mb-6">
      {error && (
        <div className="bg-red-100 text-red-700 p-2 rounded mb-4">{error}</div>
      )}
      <input
        type="text"
        name="question"
        placeholder="问题"
        className="p-1 border rounded w-full"
        required
        onChange={(e) => setQuestion(e.target.value)}
        value={question}
      />
      <input
        type="text"
        name="answer"
        placeholder="答案"
        className="p-1 border rounded w-full"
        required
        onChange={(e) => setAnswer(e.target.value)}
        value={answer}
      />
      <button
        className="bg-blue-500 text-white p-1 rounded hover:bg-blue-600"
        disabled={isSubmitting}
      >
        {isSubmitting ? '添加中...' : '添加笑话'}
      </button>
    </form>
  )
}
```

完成后 UI 效果如下：
![带添加表单的 DevJoke 应用](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746356983/Screenshot_2025-05-04_at_4.06.57_AM_dkgvsm.png)

## 理解整体协作机制

解析应用各部分的协作关系：

1. **服务端函数**：运行在服务端处理数据操作

   - `getJokes`：从 JSON 文件读取笑话
   - `addJoke`：向 JSON 文件添加新笑话

2. **TanStack Router**：处理路由和数据加载

   - loader 函数在路由访问时获取笑话数据
   - `useLoaderData` 使数据在组件中可用
   - `router.invalidate()` 添加新笑话后刷新数据

3. **React 组件**：构建应用 UI

   - `JokesList`：展示笑话列表
   - `JokeForm`：提供添加新笑话的表单

4. **基于文件的存储**：在 JSON 文件中存储笑话
   - 通过 Node.js `fs` 模块读写
   - 数据在服务器重启后仍然保留

## 应用中的数据流

### 数据流程图

![数据流程图](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746437057/Screenshot_2025-05-05_at_2.23.11_AM_wxfz2m.png)

用户访问首页时：

1. 路由中的 `loader` 函数调用 `getJokes()` 服务端函数
2. 服务端读取 `jokes.json` 并返回笑话数据
3. 数据通过 `useLoaderData()` 传递给 `HomePage` 组件
4. `HomePage` 组件将数据传递给 `JokesList` 组件

用户添加新笑话时：

1. 填写并提交表单
2. `handleSubmit` 函数调用 `addJoke()` 服务端函数
3. 服务端读取当前笑话，添加新笑话后写回 `jokes.json`
4. 操作完成后调用 `router.invalidate()` 刷新数据
5. 再次触发 loader 获取更新后的笑话
6. UI 更新显示新笑话

实际效果演示：

<iframe width="560" height="315" src="https://www.youtube.com/embed/zd0rtKbtlgU?si=7W1Peoo0W0WvZmAd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 常见问题与调试

构建 TanStack Start 应用时可能遇到的常见问题及解决方案：

### 服务端函数不工作

排查步骤：

1. 检查是否使用正确的 HTTP 方法（`GET`、`POST` 等）
2. 确认文件路径正确且可访问
3. 查看服务端控制台错误信息
4. 确保没有在服务端函数中使用仅限客户端的 API

### 路由
