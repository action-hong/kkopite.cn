---
title: 使用eslint碰到的一些问题
date: 2023-08-18T11:00:00.000+00:00
lang: zh
duration: 10min
---

## 自定义 rule 时出现 Value xx should NOT have additional properties。

例如我们写一个自定义 `rule`：

```ts
export type Options = [
  {
    allow?: string[]
    label?: string
  },
]

export default createESLintRule<Options, MessageIds>({
  schema: [],
  create(ctx) {
    // todo
  },
})
```

然后我们测试该 `rule`：

```ts
const invalid = [
  {
    code: 'something code',
    output: 'other code',
    options: [{ allow: ['awesome'], label: 'awesome label' }],
  },
]

ruleTester.run(RULE_NAME, rule, {
  valid,
  invalid,
})
```

会出现如下提示：

```bash
Value {"allow": "['awesome']", "label":"awesome label"} should NOT have additional properties.
```

根本原因在于 `rule` 的配置是通过 [`scheme`](https://eslint.org/docs/latest/extend/custom-rules#options-schemas) 去限制的，因此我们不仅要写好 `Options` 的类型，同时也要写好对应的 `schema`：

```ts
createESLintRule({
  schema: [
    {
      type: 'object',
      properties: {
        allow: {
          type: 'array',
          items: {
            type: 'string',
          },
          uniqueItems: true,
        },
        label: {
          type: 'string',
        },
      },
      additionalProperties: false,
    },
  ],
})
```