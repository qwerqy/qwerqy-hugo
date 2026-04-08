---
title: Javascript Asynchronous Precendence.
date: 2025-02-20
tags:
  - javascript
comment: Deep...
---

What would the output of the following function be?

```javascript
function main() {
  setTimeout(() => console.log(3), 0)

  new Promise((resolve) => resolve(2)).then((val) => console.log(val))

  console.log(1)
}
```

`console.log(1)` runs first, `Promise` goes second and lastly `setTimeout`. This is because JavaScript has different types of queues for handling asynchronous operations:

1. **Call Stack**: Executes synchronous code immediately (`console.log(1)`)
2. **Microtask Queue**: Handles Promises and process.nextTick(), has higher priority
3. **Macrotask Queue**: Handles setTimeout, setInterval, and I/O operations

Even though both `setTimeout` and `Promise` are asynchronous operations, Promises are processed in the microtask queue which is processed immediately after the call stack is empty, while `setTimeout` callbacks go into the macrotask queue which is processed later.

This execution order is part of the JavaScript Event Loop:

1. Execute all synchronous code
2. Process all microtasks (Promises)
3. Process one macrotask (setTimeout)
4. Repeat steps 2-3

So the output will be:

```
1
2
3
```
