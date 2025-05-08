---
source-updated-at: '2025-05-05T14:23:49.000Z'
translation-updated-at: '2025-05-08T20:27:53.256Z'
id: reading-and-writing-file
title: 讀取與寫入檔案
---

本教學將引導您使用 TanStack Start 建構一個完整的全端應用程式。您將建立一個 DevJokes 應用程式，讓使用者可以瀏覽並新增開發者主題的笑話，同時示範 TanStack Start 的關鍵概念，包括伺服器函式 (server functions)、基於檔案的資料儲存以及 React 元件。

以下是應用程式的實際運作示範：

<iframe width="560" height="315" src="https://www.youtube.com/embed/zd0rtKbtlgU?si=7W1Peoo0W0WvZmAd" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

本教學的完整程式碼可在 [GitHub](https://github.com/shrutikapoor08/devjokes) 上取得。

## 您將學到

1. 設定 TanStack Start 專案
2. 實作伺服器函式 (server functions)
3. 從檔案讀取與寫入資料
4. 使用 React 元件建構完整 UI
5. 使用 TanStack Router 進行資料獲取與導航

## 必要條件

- 基本的 React 與 TypeScript 知識
- 已安裝 Node.js 與 `pnpm`

## 建議了解

- [伺服器渲染 (SSR)](https://tanstack.com/router/latest/docs/framework/react/guide/ssr)
- [TanStack Router 概念](https://tanstack.com/router/latest/docs/framework/react/routing/routing-concepts)
- [React Query 概念](https://tanstack.com/query/latest/docs/framework/react/overview)

## 設定 TanStack Start 專案

首先，建立一個新的 TanStack Start 專案：

```bash
pnpx create-start-app devjokes
cd devjokes
```

執行此腳本時，會詢問幾個設定問題。您可以選擇適合的選項，或直接按 Enter 接受預設值。

可選地，您可以傳入 `--add-on` 旗標來獲取選項，例如 Shadcn、Clerk、Convex、TanStack Query 等。

設定完成後，安裝相依套件並啟動開發伺服器：

```bash
pnpm i
pnpm dev
```

本專案需要額外安裝幾個套件：

```bash
# 安裝 uuid 以產生唯一 ID
pnpm add uuid
pnpm add -D @types/uuid
```

## 了解專案結構

此時，專案結構應如下所示：

```
/devjokes
├── src/
│   ├── routes/
│   │   ├── __root.tsx                    # 根佈局
│   │   ├── index.tsx                     # 首頁
│   │   ├── demo.start.server-funcs.tsx   # 示範伺服器函式
│   │   └── demo.start.api-request.tsx    # 示範 API 請求
│   ├── api/                              # API 端點
│   ├── components/                       # React 元件
│   ├── api.ts                            # API 處理器
│   ├── client.tsx                        # 客戶端入口點
│   ├── router.tsx                        # 路由設定
│   ├── routeTree.gen.ts                  # 產生的路由樹
│   ├── ssr.tsx                           # 伺服器渲染
│   └── styles.css                        # 全域樣式
├── public/                               # 靜態資源
├── app.config.ts                         # TanStack Start 設定
├── package.json                          # 專案相依套件
└── tsconfig.json                         # TypeScript 設定
```

這個結構一開始可能看起來很複雜，但以下是您需要關注的關鍵檔案：

1. `router.tsx` - 設定應用程式的路由
2. `src/routes/__root.tsx` - 根佈局元件，您可以在這裡新增全域樣式與元件
3. `src/routes/index.tsx` - 您的首頁
4. `client.tsx` - 客戶端入口點
5. `ssr.tsx` - 處理伺服器渲染

專案設定完成後，您可以在 `localhost:3000` 存取您的應用程式。您應該會看到預設的 TanStack Start 歡迎頁面。

此時，您的應用程式會如下所示：

![TanStack Start 設定完成後的歡迎頁面](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746312482/Photo-1_lpechn.png)

## 步驟 1：從檔案讀取資料

讓我們從建立一個基於檔案的儲存系統來儲存笑話開始。

### 步驟 1.1：建立包含笑話的 JSON 檔案

設定一個笑話清單，供我們在頁面上渲染。在專案根目錄建立一個 `data` 目錄，並在其中建立一個 `jokes.json` 檔案：

```bash
mkdir -p src/data
touch src/data/jokes.json
```

現在，讓我們新增一些範例笑話到這個檔案：

```json
[
  {
    "id": "1",
    "question": "為什麼鍵盤不睡覺？",
    "answer": "因為它們有兩班制 (two shifts)"
  },
  {
    "id": "2",
    "question": "你是 RESTful API 嗎？",
    "answer": "因為你 GET 我的注意力，PUT 一些愛，POST 最可愛的微笑，並 DELETE 我的壞日子"
  },
  {
    "id": "3",
    "question": "我以前知道一個關於 Java 的笑話",
    "answer": "但我記憶體不足了。"
  },
  {
    "id": "4",
    "question": "為什麼前端開發者獨自吃午餐？",
    "answer": "因為他們不知道如何 join 表格 (tables)。"
  },
  {
    "id": "5",
    "question": "我要宣戰。",
    "answer": "var war;"
  }
]
```

### 步驟 1.2：為資料建立型別

建立一個檔案來定義我們的資料型別。在 `src/types/index.ts` 建立一個新檔案：

```typescript
// src/types/index.ts
export interface Joke {
  id: string
  question: string
  answer: string
}

export type JokesData = Joke[]
```

### 步驟 1.3：建立讀取檔案的伺服器函式

建立一個新檔案 `src/serverActions/jokesActions.ts` 來建立一個伺服器函式，執行讀寫操作。我們將使用 [`createServerFn`](https://tanstack.com/start/latest/docs/framework/react/server-functions) 建立伺服器函式。

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

在這段程式碼中，我們使用 `createServerFn` 建立一個伺服器函式來從 JSON 檔案讀取笑話。`handler` 函式中，我們使用 `fs` 模組來讀取檔案。

### 步驟 1.4：在客戶端使用伺服器函式

現在要使用這個伺服器函式，我們可以直接在程式碼中呼叫它，使用 TanStack Router（已內建於 TanStack Start 中）！

現在讓我們建立一個新元件 `JokesList`，用 Tailwind 樣式在頁面上渲染笑話。

```tsx
// src/components/JokesList.tsx
import { Joke } from '../types'

interface JokesListProps {
  jokes: Joke[]
}

export function JokesList({ jokes }: JokesListProps) {
  if (!jokes || jokes.length === 0) {
    return <p className="text-gray-500 italic">找不到笑話。新增一些吧！</p>
  }

  return (
    <div className="space-y-4">
      <h2 className="text-xl font-semibold">笑話收藏</h2>
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

現在讓我們在 `App.jsx` 中使用 TanStack Router 呼叫我們的伺服器函式：

```jsx
// App.jsx
import { createFileRoute } from '@tanstack/react-router'
import { getJokes } from './serverActions/jokesActions'
import { JokesList } from './JokesList'

export const Route = createFileRoute('/')({
  loader: async () => {
    // 當路由被存取時載入笑話資料
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

當頁面載入時，`jokes` 已經會包含來自 `jokes.json` 檔案的資料！

加上一些 Tailwind 樣式後，應用程式應該如下所示：

![包含 5 個 DevJokes 的 DevJoke 應用程式](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746313558/Screenshot_2025-05-03_at_4.04.50_PM_w0eern.png)

## 步驟 2：將資料寫入檔案

目前為止，我們已經能成功從檔案讀取資料！我們可以使用相同的方法，透過 `createServerFunction` 將資料寫入 `jokes.json` 檔案。

### 步驟 2.1：建立寫入檔案的伺服器函式

現在是時候修改 `jokes.json` 檔案，讓我們可以新增笑話到其中。讓我們建立另一個伺服器函式，但這次使用 `POST` 方法來寫入同一個檔案。

```tsx
// src/serverActions/jokesActions.ts
import { createServerFn } from '@tanstack/react-start'
import * as fs from 'node:fs'
import { v4 as uuidv4 } from 'uuid' // 新增這個 import
import type { Joke, JokesData } from '../types'

export const addJoke = createServerFn({ method: 'POST' })
  .validator((data: { question: string; answer: string }) => {
    // 驗證輸入資料
    if (!data.question || !data.question.trim()) {
      throw new Error('笑話問題是必填的')
    }
    if (!data.answer || !data.answer.trim()) {
      throw new Error('笑話答案是必填的')
    }
    return data
  })
  .handler(async ({ data }) => {
    try {
      // 從檔案讀取現有的笑話
      const jokesData = await getJokes()

      // 建立一個帶有唯一 ID 的新笑話
      const newJoke: Joke = {
        id: uuidv4(),
        question: data.question,
        answer: data.answer,
      }

      // 將新笑話新增到清單中
      const updatedJokes = [...jokesData, newJoke]

      // 將更新後的笑話寫回檔案
      await fs.promises.writeFile(
        JOKES_FILE,
        JSON.stringify(updatedJokes, null, 2),
        'utf-8',
      )

      return newJoke
    } catch (error) {
      console.error('新增笑話失敗:', error)
      throw new Error('新增笑話失敗')
    }
  })
```

在這段程式碼中：

- 我們使用 `createServerFn` 建立伺服器函式，這些函式在伺服器上執行但可以從客戶端呼叫。這個伺服器函式用於將資料寫入檔案。
- 我們首先使用 `validator` 來驗證輸入資料。這是個好習慣，確保我們接收的資料格式正確。
- 我們將在 `handler` 函式中執行實際的寫入操作。
- `getJokes` 從我們的 JSON 檔案讀取笑話。
- `addJoke` 驗證輸入資料並新增一個新笑話到我們的檔案中。
- 我們使用 `uuidv4()` 為笑話產生唯一 ID。

### 步驟 2.2：新增表單以新增笑話到 JSON 檔案

現在，讓我們修改首頁來顯示笑話並提供新增笑話的表單。讓我們建立一個新元件 `JokeForm.jsx` 並新增以下表單：

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
          placeholder="輸入笑話問題"
          className="w-full p-2 border rounded focus:ring focus:ring-blue-300 flex-1"
          value={question}
          onChange={(e) => setQuestion(e.target.value)}
          required
        />

        <input
          id="answer"
          type="text"
          placeholder="輸入笑話答案"
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
          {isSubmitting ? '新增中...' : '新增笑話'}
        </button>
      </div>
    </form>
  )
}
```

### 步驟 2.3：將表單連接到伺服器函式

現在，讓我們在 `handleSubmit` 函式中將表單連接到我們的 `addJoke` 伺服器函式。呼叫伺服器動作很簡單！就是一個函式呼叫。

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

      // 清空表單
      setQuestion('')
      setAnswer('')

      // 重新整理資料
      router.invalidate()
    } catch (error) {
      console.error('新增笑話失敗:', error)
      setError('新增笑話失敗')
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
        placeholder="問題"
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
        {isSubmitting ? '新增中...' : '新增笑話'}
      </button>
    </form>
  )
}
```

這樣，我們的 UI 應該如下所示：
![包含新增笑話表單的 DevJoke 應用程式](https://res.cloudinary.com/dubc3wnbv/image/upload/v1746356983/Screenshot_2025-05-04_at_4.06.57_AM_dkgvsm.png)

## 了解所有部分如何協同工作

讓我們分解應用程式的不同部分如何協同工作：

1. **伺服器函式**：這些在伺服器上執行並處理資料操作

   - `getJokes`：從我們的 JSON 檔案讀取笑話
   - `addJoke`：新增新笑話到我們的 JSON 檔案

2. **TanStack Router**：處理路由與資料載入

   - loader 函式在路由被存取時獲取笑話資料
   - `useLoaderData` 讓這些資料在我們的元件中可用
   - `router.invalidate()` 在新增新笑話時重新整理資料

3. **React 元件**：建構應用程式的 UI
