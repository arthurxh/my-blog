---
title: React Hooks 入门指南
published: 2024-12-23
tags: [React, Hooks, JavaScript]
category: React
draft: false
---

# React Hooks 入门指南

React Hooks 是 React 16.8 引入的新特性，它允许你在不编写 class 组件的情况下使用 state 和其他 React 特性。Hooks 让函数组件拥有了类组件的能力，同时代码更加简洁和易于维护。

## 什么是 Hooks

Hooks 是一些可以让你在函数组件里"钩入" React state 及生命周期等特性的函数。React 内置了一些常用的 Hooks，如 `useState`、`useEffect`、`useContext` 等。

## useState Hook

`useState` 是最常用的 Hook，用于在函数组件中添加 state。

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>你点击了 {count} 次</p>
      <button onClick={() => setCount(count + 1)}>
        点击我
      </button>
    </div>
  );
}
```

`useState` 返回一个数组，第一个元素是当前的 state 值，第二个元素是更新该 state 的函数。

## useEffect Hook

`useEffect` 用于处理副作用操作，如数据获取、订阅、手动修改 DOM 等。

```jsx
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetchUser(userId).then(data => setUser(data));
  }, [userId]);

  if (!user) return <div>加载中...</div>;

  return <div>欢迎, {user.name}!</div>;
}
```

`useEffect` 接受两个参数：一个函数和一个依赖数组。当依赖数组中的值发生变化时，effect 会重新执行。

## useContext Hook

`useContext` 让你可以在函数组件中读取 context 的值，而无需使用 Consumer 组件。

```jsx
import React, { useContext } from 'react';

const ThemeContext = React.createContext('light');

function Button() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>点击我</button>;
}
```

## 自定义 Hooks

你可以创建自己的 Hooks 来复用状态逻辑。

```jsx
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}

function App() {
  const { width, height } = useWindowSize();
  return <div>窗口大小: {width} x {height}</div>;
}
```

## Hooks 使用规则

使用 Hooks 时需要遵循以下规则：

1. **只在函数最外层调用 Hook**：不要在循环、条件判断或者子函数中调用
2. **只在 React 函数中调用 Hook**：在 React 函数组件或自定义 Hook 中调用

```jsx
// 错误示例
function BadExample() {
  if (condition) {
    const [count, setCount] = useState(0); // 不要在条件语句中使用
  }
}

// 正确示例
function GoodExample() {
  const [count, setCount] = useState(0);
  if (condition) {
    // 在这里使用 count
  }
}
```

## 总结

React Hooks 为函数组件带来了强大的能力，让我们能够更优雅地编写 React 组件。掌握常用的 Hooks 和自定义 Hooks 的编写，将大大提升你的 React 开发效率。
