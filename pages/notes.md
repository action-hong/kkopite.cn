---
title: Notes - kkopite
display: Notes
subtitle: 一些简短代码片段、笔记
description: 一些简短代码片段、笔记
---

[[toc]]

[RSS Feed](https://kkopite.netlify.app/notes/feed.xml)

<article>

## `markdown`代码块跳过`eslint`检查

_2022/05/18_


在写`markdown`时有时也需要写一些代码，这时候可能会使用`eslint-plugin-markdown`来校验这些代码块。但有时候可能代码只是一些示意代码，因此不会那么合法，这时候需要跳过这些代码不进行检验，可以使用`<!-- eslint-skip -->`这个注释加载不检测的代码块前面：

````markdown
<!-- eslint-skip -->

```js
// 该代码不会被校验
const obj = {
  name: 'kkopite',
  ...
}
```

````

</article>

<article>

## 使用`VS Code`编写`vue`时，自定义组件变红，且无法跳转

_2022/05/12_

首先你使用的是[`Volar`](https://github.com/johnsoncodehk/volar)这个插件

在写`vue`组件时，经常碰到引入的自定义组件标红高亮且无法进行跳转。一般用以下两种方法就能解决:

1. 更改`tsconfig.json`，给`compilerOptions`配置`"jsx": "preserve"`:

```json
{
  "compilerOptions": {
    "jsx": "preserve",
  }
}
```

2. 如果你使用`pnpm`，尝试用[shamefully-hoist](https://pnpm.io/npmrc#shamefully-hoist)模式重新安装依赖(**如果你是使用`npm`或`yarn`则忽略该方法**)

可以在你的`.npmrc`文件里添加如下配置:

```
shamefully-hoist=true
```

然后重新安装:

```bash
pnpm i
```

完成如上步骤后，重新加载`VS Code`，大概率红色高亮就没了，组件也能正常跳转。

PS: 如果还不行只好去官方的[issue](https://github.com/johnsoncodehk/volar/issues/)求救了

</article>