---
title: TypeScript的一些技巧
date: 2022-05-18T19:00:00.000+00:00
lang: zh
duration: 10min
---

[[toc]]

## 检查 `union` 的每一个类型

有时候我们会针对某个 `union` 类型做各种判断，以便进行各种处理：

```ts
type Shape = 'circle' | 'square'

function getArea(shape: Shape) {
  switch (shape) {
    case 'circle':
      return Math.PI * 2
    case 'square':
      return 4
  }
}
```

后续开发中，加了 `triangle` 的类型，可能会忘记同步修改 `getArea` 方法，这时候我们可以用 `never` 来提示开发者：

```ts
type Shape = 'circle' | 'square' | 'triangle'

function getArea(shape: Shape) {
  switch (shape) {
    case 'circle':
      return Math.PI * 2
    case 'square':
      return 4
    default: {
      // Type 'string' is not assignable to type 'never'.ts(2322)
      const _exhaustiveCheck: never = shape
      return _exhaustiveCheck
    }
  }
}
```

这里编译器就会提示错误：`'string'` 无法赋值给 `'never'`，提醒用户还需要处理 `triangle` 这个 case，这样子我们就不担心后续增加新类型而忘记添加相关的处理方法了。

另外我们可以使用 `satisfies` 也能达到类似的效果

```ts
function getArea2(shape: Shape) {
  switch (shape) {
    case 'circle':
      return Math.PI * 2
    case 'square':
      return 4
  }
  // Type 'string' does not satisfy the expected type 'never'.ts(1360)
  // https://twitter.com/mattpocockuk/status/1691014244609765376
  shape satisfies never
}
```



## [`tsup`](https://tsup.egoist.dev/) 打包时排除依赖

[`tsup`](https://tsup.egoist.dev/) 打包时会默认排除掉 `package.json` 的 `dependencies` 和 `peerDependencies`，因此如果需要将某些依赖打包到生成文件的话，使用 [`noExternal`](https://tsup.egoist.dev/#excluding-all-packages) 配置

## 字面量字符串与 String 联合类型时丢失

我们经常使用联合类型来帮助我们限制函数的入参以及代码提示，如：

```ts
type IconSize = 'sm' | 'xs'

declare function foo(size: IconSize): void
```

这样在调用 `foo` 时就可以得到提示 `sm` 或 `xs` 了。

但同时我们又希望他可以输入其他字符串，于是我们将类型改成如下：

```ts
type IconSize = 'sm' | 'xs' | string
```

这样做虽然任何字符串都可以传入，但是 `sm` 和 `xs` 的提示都没掉了，究其原因是 `TypeScript` 认为 `sm` 和 `xs` 都是 `string`，因此会自动把 `type IconSize = 'sm' | 'xs' | string` 处理成 `type IconSize = string`，自然就没有提示了。

因此想做到我们的需求可以改成如下这样子：

```ts
// https://twitter.com/mpocock1/status/1506607945445949446
type IconSize = 'sm' | 'xs' | Omit<string, 'sm' | 'xs'>
```

或者这样：

```ts
type IconSize1 = 'sm' | 'xs' | (string & {})
```