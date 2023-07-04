---
title: TypeScript 4.9更新内容的学习
date: 2023-07-04T15:00:00.000+00:00
lang: zh
duration: 2 min
---

## `satisfies` 操作符

写`ts`时，有时候我们希望表达式能与某个类型匹配，同时又能有准确的推断能力：

```ts
type Colors = 'red' | 'green' | 'blue'
type RGB = [red: number, green: number, blue: number]

const palette: Record<Colors, string | RGB> = {
  red: [255, 0, 0],
  green: '#00ff00',
  blue: [0, 0, 255],
}
```

如上`palette`的键值均有约束，但是对于每个属性值无法做出准确地推断：

```ts
// 期望是string类型，但会得到 string | RGB
palette.green

// 同理期望 期望RGB
palette.red
```

而如果我们去掉`Record<Colors, string | RGB>`后，上诉推断是可以生效了，但声明的时候就无法保证匹配想要的类型的：

```ts
const a = {
  // 不小心打错了，也不会报错
  belu: '#ffffff',
}
```

而`satisfies`可以完美地解决如上两个问题：

```ts
const palette = {
  red: [255, 0, 0],
  green: '#00ff00',
  bleu: [0, 0, 255],
//  ~~~~ bleu 是个非法的key
} satisfies Record<Colors, string | RGB>

// 是个数组
const redComponent = palette.red.at(0)
// 是个字符串
const greenNormalized = palette.green.toUpperCase()
```

## `in`操作符收窄类型

`in`操作符可以用来判断某个对象是否有某个key，而也可以用来收窄类型：

```ts
interface RGB {
  red: number
  green: number
  blue: number
}
interface HSV {
  hue: number
  saturation: number
  value: number
}
function setColor(color: RGB | HSV) {
  if ('hue' in color) {
    // 此时可以收窄到 HSV类型
  }
  // ...
}
```

再过去，但是对于哪些没有在类型中列出来的属性，则无法做收窄, 会报错

```ts
function foo(package: unknown) {
  if (typeof package === 'object') {
    if ('name' in package && typeof package.name === 'string') {
      // error
      package.name
    }
  }
}
```

`package`被收窄到`object`, 而`in`会严格收窄到实际有列出要检查属性的类型，因此还是`object`

而在4.9版本，在做`in`判断时，如果检查的属性是什么再类型里列出来的，则会将类型收窄成`object & Record<'name', unknown>`, 然后判断`typeof package.name === 'string'`后，进一步收窄到`object & Record<'name', string>`

同时对`in`还做了进一步检查，`in`的左侧可分配的类型是`string | number | symbol`，右侧分配给`object`，避免意外检查原始类型

- [相关的PR](https://github.com/microsoft/TypeScript/pull/50666)
- [详细介绍](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-4-9.html)
- [代码](https://www.typescriptlang.org/play?target=9&ts=5.1.3#code/C4TwDgpgBAwg9gGzgJwM5QLxQOTIgE2ygB8cAjBAVwiNOwHM8IA7bAKFEigCUBxAIUxQA2s0oBbMhGQAaKGMnS5CqcgC6bNgGM4zVMChgAhggjBg0LAG82UO1Dz4AXCIBMAVndyADD7Uzbe0YIFhcAIgBib28AMxjosID7KApqJ2FfKEyPd39AuwoIShcMnzkcjQB6SrsAPXraqFQjLTxCyigAWigAdxoAN2hxI3xoIyhOOABCNgBfJqNgAEtUGKWIdG4IHWR8AB54JDQ5PkFSfWQl5noAPgBuTWqoAHVoHqNmA2A4FLHCiZ+lFQY2QyCMICg4jMAAs4Ph0LocI5sAA6NHaXT6BwEeDiMC6FgGLDGUzmCAoxwoxYACm8AEoHk8UE1gJdrpCYXCEcwcMEWKj0To9AY+cwAHIoYYIJYALwIQhJZgsKNFKO+AFUwJBkDAjMDqQzHjUrlBAGymgBCvNgxSjMLTLRExOBwalwMgAKxcrrd22AdKgNmSSxiUGp2GYRihRBNXqgADJYxNwBA4MGvSjw1DMBgsNgLld6Ng-QHkvYnhAAB6QO0stn0ROQfIltMZ8l4SA01x0xuzOaaUlQcZYACMRqgJsAz8qAcvlAKP6gG8MwD0Zl6fWwnoAz3RnC-kElUJBr+d3qBAkkQbCDIbDEZoY55RiLmh7K5q2gQevQAAVpKhEcW7I2WloNi-ZB5EvFw82uRshQuSg7RQakWzA1l8zvEt7GAaEVnTS8hBbbtewxYVDBwiAeigD80F0UMAGsqLgMAlgsQs2DALCoTYIA)
