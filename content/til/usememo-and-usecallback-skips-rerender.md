---
title: useMemo & useCallback skips re-render
date: 2024-12-07
tags:
  - typescript
  - react
comment: Use only when necessary
---

The `useMemo` and `useCallback` hooks in React help optimize performance by preventing unnecessary re-renders.
`useMemo` memoizes the result of a computation, ensuring the value is only recalculated when its dependencies change.
`useCallback` memoizes a function reference, preventing child components from re-rendering when the function is passed as a prop unless its dependencies change.
Together, they help minimize redundant rendering, especially in components with heavy computations or deep prop trees.
