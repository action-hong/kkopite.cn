---
title: jest遇到的一些问题
date: 2023-02-14T15:00:00.000+00:00
lang: zh
duration: 5min
---

## toThrow 无法捕捉的错误

```js
function foo() {
  throw new Error('error')
}

expect(foo()).toThrow()
```

如下写法是错误的，这样 `jest` 捕捉到错误，传入 expect 的参数应该是函数，而不是将函数执行传入：

```js
expect(foo).toThrow()
```