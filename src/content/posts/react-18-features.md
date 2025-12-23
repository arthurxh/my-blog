---
title: React 18 新特性详解
published: 2024-12-23
tags: [React, React18, Features]
category: React
draft: false
---

# React 18 新特性详解

React 18 是 React 的一个重要版本更新，带来了许多令人兴奋的新特性和改进。本文将详细介绍 React 18 的主要新特性，帮助你更好地理解和使用这些功能。

## 并发渲染（Concurrent Rendering）

并发渲染是 React 18 的核心特性，它允许 React 准备多个版本的 UI 同时进行。这意味着 React 可以中断渲染过程，优先处理更重要的更新，然后再恢复被中断的渲染。

```jsx
// 启用并发模式
import { createRoot } from 'react-dom/client';

const container = document.getElementById('root');
const root = createRoot(container);
root.render(<App />);
```

## 自动批处理（Automatic Batching）

React 18 改进了批处理机制，现在可以在更多场景下自动批处理状态更新，减少不必要的重新渲染。

```jsx
// React 17: 会导致两次渲染
function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
}

// React 18: 自动批处理，只渲染一次
function handleClick() {
  setCount(c => c + 1);
  setFlag(f => !f);
}

// 即使在异步操作中也能自动批处理
function handleClick() {
  fetchSomething().then(() => {
    setCount(c => c + 1);
    setFlag(f => !f);
  });
}
```

## 新的 Hooks

### useTransition

`useTransition` 用于标记非紧急的状态更新，让 React 优先处理更重要的更新。

```jsx
import { useTransition } from 'react';

function SearchComponent() {
  const [isPending, startTransition] = useTransition();
  const [input, setInput] = useState('');
  const [list, setList] = useState([]);

  const handleChange = (e) => {
    const value = e.target.value;
    setInput(value);

    startTransition(() => {
      const filtered = largeList.filter(item => item.includes(value));
      setList(filtered);
    });
  };

  return (
    <div>
      <input value={input} onChange={handleChange} />
      {isPending && <div>加载中...</div>}
      <ul>{list.map(item => <li key={item}>{item}</li>)}</ul>
    </div>
  );
}
```

### useDeferredValue

`useDeferredValue` 用于延迟更新某个值，类似于防抖，但由 React 内部优化。

```jsx
import { useDeferredValue } from 'react';

function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);

  return (
    <div>
      <Results query={deferredQuery} />
    </div>
  );
}
```

### useId

`useId` 用于生成唯一的 ID，特别适用于可访问性属性。

```jsx
import { useId } from 'react';

function Form() {
  const id = useId();

  return (
    <div>
      <label htmlFor={id}>邮箱</label>
      <input id={id} type="email" />
    </div>
  );
}
```

## Suspense 改进

React 18 对 Suspense 进行了重要改进，现在支持服务端渲染。

```jsx
import { Suspense } from 'react';

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <DataComponent />
    </Suspense>
  );
}
```

## 严格模式变化

React 18 的严格模式会双重调用组件的 effects，帮助开发者发现副作用问题。

```jsx
// 开发模式下，effect 会被调用两次
useEffect(() => {
  console.log('Effect 被调用');
  return () => {
    console.log('Cleanup 被调用');
  };
}, []);
```

## 新的客户端和服务器 API

### createRoot

新的 `createRoot` API 替代了旧的 `render` 方法。

```jsx
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

### hydrateRoot

用于服务端渲染的 hydration。

```jsx
import { hydrateRoot } from 'react-dom/client';

hydrateRoot(document.getElementById('root'), <App />);
```

## 移除的 API

React 18 移除了一些过时的 API：

- `ReactDOM.render` → 使用 `createRoot`
- `ReactDOM.hydrate` → 使用 `hydrateRoot`
- `unmountComponentAtNode` → 使用 `root.unmount()`
- `render` 的回调参数

## 流式服务端渲染（Streaming SSR）

React 18 支持流式服务端渲染，允许服务器逐步发送 HTML 片段。

```jsx
import { renderToPipeableStream } from 'react-dom/server';

app.get('/', (req, res) => {
  const stream = renderToPipeableStream(<App />, {
    onShellReady() {
      res.statusCode = 200;
      res.setHeader('Content-type', 'text/html');
      stream.pipe(res);
    },
  });
});
```

## 总结

React 18 带来了许多重要的改进和新特性，特别是并发渲染和自动批处理，这些都将显著提升 React 应用的性能和用户体验。升级到 React 18 后，你可以逐步采用这些新特性，让应用更加高效和流畅。

注意：React 18 是向后兼容的，你可以逐步迁移，不必一次性更新所有代码。
