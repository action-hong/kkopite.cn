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

## UnionToIntersection

```ts
type UnionToIntersection<T> =
  (T extends any ? (arg: T) => any : never) extends (arg: infer R) => any
    ? R
    : never
```

核心就是函数参数的逆变

`(a: 1) => any | (a: 2) => any` 能赋值给 `(a: 1 & 2) => any`，由此可以推断出交叉类型

## UnionToTuple

```ts
type LastInUnion<T> =
  UnionToIntersection<
  T extends any ? (arg: T) => any : never
  > extends (arg: infer R) => any
    ? R
    : never

type UnionToTuple<T, B = LastInUnion<T>> =
  [T] extends [never]
    ? []
    : [...UnionToTuple<Exclude<T, B>>, B]
```

这里的 `LastInUnion` 可以从传入的 union type 中取出最后一个类型，他的实现基于下面两点前提：

1. 函数的交叉类型会形成一个重载函数，因此生成的这个重载函数的第一个参数可以是传入的 union type 中任意一个
2. 如果需要获取重载函数的相关类型，则会取重载函数的最后一个签名

参见实验 [playground](https://www.typescriptlang.org/play?#code/PTAEhK5RJ5Ubx9Gj1QjfUAhGhZxML7xhfxUA6mBYAUAFwE8AHAU1ADEBGUAXlAAoBDALlADsBXAWwCNSATgEo6APlBN2hPETKUATHUatQAZ3wCAluwDmI2uMnSCJchQDMS6qABkCvHgAmpAMYAbJgPIA3T6ABmAPaBbBYOuEGBDFRCeJEMAOQAFqRuboEJQkA)


## IsAny

```ts
// https://stackoverflow.com/questions/49927523/disallow-call-with-any/49928360#49928360
export type IsAny<T> = 0 extends (1 & T) ? true : false
```

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

## IndexOf

```ts
type IndexOf<T extends unknown[], U, C extends unknown[] = []> =
  C['length'] extends T['length']
    ? -1
    : Equal<U, T[C['length']]> extends true
      ? C['length']
      : IndexOf<T, U, [...C, 0]>
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

## Fibonacci

```ts
type Fibonacci<T extends number, Index extends any[] = [1], Prev extends any[] = [], Curr extends any[] = [1]> =
  Index['length'] extends T
    ? Curr['length']
    : Fibonacci<T, [1, ...Index], Curr, [...Prev, ...Curr]>
```

## Zip

```ts
// type exp = Zip<[1, 2], [true, false]> // expected to be [[1, true], [2, false]]
// https://github.com/type-challenges/type-challenges/issues/5619
type Zip<T extends any[], U extends any[], R extends any[] = []> =
  R['length'] extends T['length'] | U['length']
    ? R
    : Zip<T, U, [...R, [T[R['length']], U[R['length']]]]> // 妙呀
```

## is tuple

```ts
type IsTuple<T> =
  [T] extends [never] ? false
    : T extends readonly any[]
      ? number extends T['length'] // tuple的话，T['length']是个 字面量数字 这个判断就不满足了
        ? false
        : true
      : false
```

## Fill

```ts
// 自己想的, 不够精简
type Fill<
  T extends unknown[],
  N,
  Start extends number = 0,
  End extends number = T['length'],
  R extends unknown[] = [],
  S extends 0 | 1 = 0, // 0: unchanged ，1 fill it
> =
  Start extends End
    ? T
    : R['length'] extends T['length']
      ? R
      : R['length'] extends Start
        ? Fill<T, N, Start, End, [...R, N], 1>
        : R['length'] extends End
          ? Fill<T, N, Start, End, [...R, T[R['length']]], 0>
          : S extends 1
            ? Fill<T, N, Start, End, [...R, N], 1>
            : Fill<T, N, Start, End, [...R, T[R['length']]], 0>

// 参考的答案
type Fill<
  T extends unknown[],
  N,
  Start extends number = 0,
  End extends number = T['length'],
  Count extends any[] = [],
  Flag extends boolean = Count['length'] extends Start ? true : false,
> = Count['length'] extends End
  ? T
  : T extends [infer R, ...infer U]
    ? Flag extends false
      ? [R, ...Fill<U, N, Start, End, [...Count, 0]>]
      : [N, ...Fill<U, N, Start, End, [...Count, 0], Flag>]
    : T
```

## Construct Tuple

```ts
type ConstructTupleUnit<L extends number | string, R extends unknown[] = []> = `${R['length']}` extends `${L}` ? R : ConstructTupleUnit<L, [unknown, ...R]>
type X10<T extends unknown[]> = [
  ...T,
  ...T,
  ...T,
  ...T,
  ...T,
  ...T,
  ...T,
  ...T,
  ...T,
  ...T,
]

// pass 1000
type ConstructTuple<L extends number | string, R extends unknown[] = []> =
    `${L}` extends `${infer F}${infer Rest}`
      ? ConstructTuple<Rest, [...ConstructTupleUnit<F>, ...X10<R>]>
      : R
```

## Number Range

```ts
type CTuple<N extends number, R extends unknown[] = []> =
  N extends R['length'] ? R : CTuple<N, [...R, unknown]>

type NumberRange<L extends number, H extends number, R extends unknown[] = CTuple<L>, RES = L | H> =
  H extends R['length']
    ? RES
    : NumberRange<L, H, [...R, unknown], R['length'] | RES>
```

切记不要写成如下：

```ts
type CTuple<N extends number, R extends unknown[] = []> =
  N extends R['length'] ? R : CTuple<N, [...R, unknown]>

type NumberRange<L extends number, H extends number, R extends unknown[] = CTuple<L>> =
  H extends R['length']
    ? H
    : R['length'] | NumberRange<L, H, [...R, unknown]>
```

> 估计是尾部多了 `R['length']` 没法尾递归优化，导致栈溢出 (纯属猜测)

## Check Repeat Char

```ts
type CheckRepeatedChars<T extends string, R extends string = ''> = // R 表示已经遍历过的
  T extends `${infer F}${infer Rest}`
    ? F extends R
      ? true
      : CheckRepeatedChars<Rest, R | F> // 更新遍历过的
    : false

// https://github.com/type-challenges/type-challenges/issues/28150
type CheckRepeatedChars<T extends string> =
  T extends `${infer F}${infer Rest}`
    ? Rest extends `${string}${F}${string}` // 判断后续的有没有匹配这个 F
      ? true
      : CheckRepeatedChars<Rest>
    : false
```

## Class Public Key


```ts
// https://github.com/type-challenges/type-challenges/issues/21570
type ClassPublicKeys<A> = keyof A
```

## ObjectFromEntries

```ts
// 自己写的，写复杂了
type ObjectFromEntries<T> = Debug<UnionToIntersection<T extends [infer K extends string, infer V] ? { [P in K]: V } : never>>

// https://github.com/type-challenges/type-challenges/issues/3382
type ObjectFromEntries<E extends [string, unknown]> = { // 秒呀
  [K in E as K[0]]: K[1]
}
```

## IsPalindrome

```ts
type Reverse<T extends string, R extends string = ''> =
  T extends `${infer H}${infer Rest}`
    ? Reverse<Rest, `${H}${R}`>
    : R
type IsPalindrome<T extends string | number, S extends string = `${T}`> = S extends Reverse<S> ? true : false

// https://github.com/type-challenges/type-challenges/issues/4093
type IsPalindrome<T extends string | number, K = `${T}`> =
  K extends `${infer L}${infer R}` ?
    R extends '' ? true : // 判断为单个字符，那必然回文
      K extends `${L}${infer S}${L}` ? IsPalindrome<S> : false // 判断头尾相同, 进入下一个检查
    : true
```

## Binary To Decimal

```ts
// 从右向左，复杂了
type Reverse<T extends string> = T extends `${infer F}${infer R}` ? `${Reverse<R>}${F}` : ''

type BinaryToDecimal<
  S extends string,
  T extends string = Reverse<S>,
  Index extends any[] = [1], // 1, 2, 4, 8, ...
  Acc extends any[] = []> = // Acc 为累加
    T extends `${infer F}${infer R}`
      ? F extends '0'
        ? BinaryToDecimal<S, R, [...Index, ...Index], Acc> // Index * 2 进位
        : BinaryToDecimal<S, R, [...Index, ...Index], [...Index, ...Acc]>
      : Acc['length']

// https://github.com/type-challenges/type-challenges/issues/6349
// 从左向右算 累加乘2，碰到1就加1
type BinaryToDecimal<
  S extends string,
  R extends any[] = [],
> =
  S extends `${infer F}${infer L}`?
    F extends '0'? BinaryToDecimal<L, [...R, ...R]>:BinaryToDecimal<L, [...R, ...R, 1]> // 从左到右算好像更快？不断乘2，碰到有1的就加上1
    :R['length']
```

## Slice

```ts
type NArray<N extends number, R extends any[] = []> =
  N extends R['length']
    ? R
    : NArray<N, [...R, 1]>

type Minus<A extends number, B extends number> =
  NArray<A> extends [...NArray<B>, ...infer Rest]
    ? Rest['length']
    : -1

type NegativeToPositive<N extends number, L extends number> =
  `${N}` extends `-${infer NN extends number}`
    ? Minus<L, NN> // 负数 + L, 有局限性
    : N

type Slice<
  Arr extends any[],
  _Start extends number = 0,
  _End extends number = Arr['length'],
  // 保证是正数
  Start extends number = NegativeToPositive<_Start, Arr['length']>,
  End extends number = NegativeToPositive<_End, Arr['length']>,
  Acc1 extends any[] = [],
  Acc2 extends any[] = [],
  R extends any[] = [],
> =
  Minus<End, Start> extends -1 ? R // End 小于 Start
    : Start extends End
      ? R
      : Arr extends [infer F, ...infer Rest]
        ? Start extends Acc1['length'] // 到了
          ? End extends Acc2['length'] // 结束
            ? R
            : Slice<Rest, _Start, _End, Start, End, Acc1, [...Acc2, 0], [...R, F]> // 开始添加
          : Slice<Rest, _Start, _End, Start, End, [...Acc1, 0], [...Acc2, 0], R>
        : R
```

思路就是 2 点：

1. 正数转为负数
2. 计算跳过前面 start 个，开始加入，到 end 时候停

看别人的答案，更为巧妙：

```ts
// https://github.com/type-challenges/type-challenges/issues/22110
// N 如果是负的，转换为 正确的正数位置
type ToPositive<N extends number, Arr extends unknown[]> =
  `${N}` extends `-${infer P extends number}`
    ? Slice<Arr, P>['length'] // 秒呀，假设数组长度 4，输入-1，则slice(1)的数组长度为3，刚好就是对应的正确的正数位置
    : N

// get the initial N items of Arr
// 等价 array.slice(0, n)
type InitialN<Arr extends unknown[], N extends number, _Acc extends unknown[] = []> =
  _Acc['length'] extends N | Arr['length']
    ? _Acc
    : InitialN<Arr, N, [..._Acc, Arr[_Acc['length']]]>

// 得出 [0, end) 和 [0, start) infer 出 [start, end)
type Slice<Arr extends unknown[], Start extends number = 0, End extends number = Arr['length']> =
  InitialN<Arr, ToPositive<End, Arr>> extends [...InitialN<Arr, ToPositive<Start, Arr>>, ...infer Rest]
    ? Rest
    : []
```