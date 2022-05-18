---
title: TypeScript的一些技巧
date: 2022-05-18T19:00:00.000+00:00
lang: zh
duration: 10min
---

[[toc]]

## 字面量字符串与String联合类型时丢失

我们经常使用联合类型来帮助我们限制函数的入参以及代码提示，如：

```ts
type IconSize = 'sm' | 'xs'

declare function foo(size: IconSize): void
```

这样在调用`foo`时就可以得到提示`sm`或`xs`了。

但同时我们又希望他可以输入其他字符串，于是我们将类型改成如下：

```ts
type IconSize = 'sm' | 'xs' | string
```

这样做虽然任何字符串都可以传入，但是`sm`和`xs`的提示都没掉了，究其原因是`TypeScript`认为`sm`和`xs`都是`string`，因此会自动把`type IconSize = 'sm' | 'xs' | string`处理成`type IconSize = string`，自然就没有提示了。

因此想做到我们的需求可以改成如下这样子：

```ts
// https://twitter.com/mpocock1/status/1506607945445949446
type IconSize = 'sm' | 'xs' | Omit<string, 'sm' | 'xs'>
```