---
title: 使用 ESLint 一些记录
date: 2024-09-02T13:00:00.000+00:00
lang: zh
duration: 5 min
---

## HTML 属性强制一个一行

期望如下，每个属性一行：

```html
<div
  class="foo"
  style="color: red"
>
  hello worlhhd
</div>
```

### vue 

配置 [vue/max-attributes-per-line](https://eslint.vuejs.org/rules/max-attributes-per-line.html#vue-max-attributes-per-line) 规则

```js
export default {
  rules: {
    'vue/max-attributes-per-line': ['error', {
      // 默认都是1，表示一行内最多有多少个属性
      singleline: 1,
      multiline: 1,
    }],
  },
}
```

`singleline` 表示会去检查开始标签都在同一行的情况 (即 `<div` 和 `/>` 或 `>`)，如：

```html
<div class="foo" style="color: red" />
<div class="foo" style="color: red">hello</div>
```

`multiline` 表示会去检查开始标签不在同一行的情况，如：

```html
<div 
  class="foo" style="color: red" 
/>
```

### react

配置 [jsx/jsx-max-props-per-line](https://eslint.style/rules/jsx/jsx-max-props-per-line)

```js
export default {
  rules: {
    'style/jsx-max-props-per-line': ['error', { maximum: 1, when: 'always' }],
  },
}
```

- `maximum` 表示一行最多有多少个属性
- `when` 有2个取值
  - `always` 即等同于 `vue/max-attributes-per-line` 中设置 `singleline` 为 1，`multiline` 为 1
  - `multiline` 即只检查 `multiline` 的情况，不会去检查 `singleline` 的情况

> 或者这个 [`react/jsx-max-props-per-line`](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-max-props-per-line.md)

### HTML

`HTML` 用 `prettier` 的 [`single-attribute-per-line`](https://prettier.io/docs/en/options#single-attribute-per-line)

> 该配置也适用于 `vue`、`jsx` (如果用 `prettier` 的话)

---

### 总结

可以使用 antfu 提供的 [`eslint-config`](https://github.com/antfu/eslint-config):

```js
import antfu from '@antfu/eslint-config'

export default antfu({
  formatters: {
    html: true,
    prettierOptions: {
      singleAttributePerLine: true,
    },
  },
  vue: true,
  react: true,
  rules: {
    'vue/max-attributes-per-line': ['error', {
      // 默认都是1
      singleline: 1,
      multiline: 1,
    }],
    'style/jsx-max-props-per-line': ['error', { maximum: 1, when: 'always' }],
  },
})
```