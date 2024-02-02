---
title: TS 类型的一些有趣写法
date: 2024-02-02T10:00:00.000+00:00
lang: zh
duration: 10 min
---

年底最后几天没啥事干，刷了下 [type-chanllenges](https://github.com/type-challenges/type-challenges) 记录些有趣的解法

## Equal

```ts
export type Equal<X, Y> =
  (<T>() => T extends X ? 1 : 2) extends
  (<T>() => T extends Y ? 1 : 2) ? true : false
```

- [出处](https://github.com/microsoft/TypeScript/issues/27024#issuecomment-421529650)
- https://stackoverflow.com/questions/68961864/how-does-the-equals-work-in-typescript/68963796#68963796
- https://github.com/type-challenges/type-challenges/discussions/9100

## Last of Array

```ts
// https://github.com/type-challenges/type-challenges/issues/100
type Last<T extends any[]> = [any, ...T][T['length']]
```

## Fist of Array

```ts
type First<T extends any[]> = T['length'] extends 0 ? never : T[0]
type First<T extends any[]> = T extends [] ? never : T[0]
type First<T extends any[]> = '0' extends keyof T ? T[0] : never
type First<T extends any[]> = T[number] extends never ? never : T[0]
```

## LastIndexOf

```ts
// https://github.com/type-challenges/type-challenges/issues/5330
type LastIndexOf<T extends any[], U> =
  T extends [...infer I, infer Tail]
    ? Equal<Tail, U> extends true
      ? I['length'] // 秒呀！！！！
      : LastIndexOf<I, U>
    : -1
```

## Tuple To Union

```ts
// https://github.com/type-challenges/type-challenges/issues/7
type TupleToUnion<T extends any[]> = T[number]
type TupleToUnion<T extends any[]> = T extends Array<infer I> ? I : never
```

## Tirm

```ts
// https://github.com/type-challenges/type-challenges/issues/481
type Space = ' ' | '\t' | '\n'

type Trim<S extends string> = S extends `${Space}${infer T}` | `${infer T}${Space}` ? Trim<T> : S
```

## Replace

```ts
// https://github.com/type-challenges/type-challenges/issues/407
type Replace<S extends string, From extends string, To extends string> =
  From extends ''
    ? S
    : S extends `${infer V}${From}${infer R}`
      ? `${V}${To}${R}`
      : S

type ReplaceAll<S extends string, From extends string, To extends string> =
  From extends ''
    ? S
    : S extends `${infer V}${From}${infer R}`
      ? `${V}${To}${ReplaceAll<R, From, To>}`
      : S
```

## Permutation

```ts
type Permutation<T, K = T> =
  [T] extends [never] // 第一步排除 never
    ? []
    : T extends any //  Distributive Conditional Types  不能放在第一步，否则 never 传进来时，认为是没有一个类型的union type，就直接返回never了
      ? [T, ...Permutation<Exclude<K, T>>]
      : []
```

## KebabCase

```ts

type U = 'A'|'B'|'C'|'D'|'F'|'E'|'G'|'H'|'I'|'J'|'K'|'L'|'M'|'N'|'O'|'P'|'Q'|'R'|'S'|'T'|'U'|'V'|'W'|'X'|'Y'|'Z'

type KebabCase<S extends string, Perfix extends string = ''> =
  S extends `${infer F}${infer T}`
    ? F extends U
      ? `${Perfix}${Lowercase<F>}${KebabCase<T, '-'>}`
      : `${F}${KebabCase<T, '-'>}`
    : S

// https://github.com/type-challenges/type-challenges/issues/664
type KebabCase<S extends string> = S extends `${infer S1}${infer S2}`
  ? S2 extends Uncapitalize<S2>
    ? `${Uncapitalize<S1>}${KebabCase<S2>}`
    : `${Uncapitalize<S1>}-${KebabCase<S2>}`
  : S
```

## Diff

```ts
// https://github.com/type-challenges/type-challenges/issues/3014
// keyof (O | O1) 拿到他两共有的属性值
type Diff<O, O1> = Omit<O & O1, keyof (O | O1)>
```

## AnyOf

```ts
// https://github.com/type-challenges/type-challenges/issues/954
type AnyOf<T extends any[]> = T[number] extends 0 | '' | false | [] | Record<string, never>
  ? false : true
```

## IsUnion

```ts
// https://github.com/type-challenges/type-challenges/issues/1140
type IsUnionImpl<T, C extends T = T> = (T extends T ? C extends T ? true : unknown : never) extends true ? false : true
type IsUnion<T> = IsUnionImpl<T>
```