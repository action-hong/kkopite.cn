---
title: 一些有趣的算法的集合
date: 2022-11-25T15:00:00.000+00:00
lang: zh
duration: 2 min
---

[[toc]]

## 计算一个二进制数它含有的 1 的个数

[出处](http://www.robalni.org/posts/20220428-counting-set-bits-in-an-interesting-way.txt)

```js
function calc(num) {
  let remaining = num
  let sum = 0
  while (remaining) {
    remaining >>= 1
    sum += remaining
  }
  return num - sum
}
```

迭代过程中，sum 不断累积，其值可看做是 `num / 2 + num / 4 + ...`，如果是实数的话，则 `sum === num`，但是对于整数来说，每次右移时，如果第 0 位是 1 的话，就会被去掉，**导致 sum 的值比 num 少 1**，显然累加结束后，`num - sum` 的值即为 `num` 的二进制表达式中 `1` 的个数。

代码简化一下：

```js
function calc(num) {
  let diff = num
  while (num) {
    num >>= 1
    diff -= num
  }
  return diff
}
```