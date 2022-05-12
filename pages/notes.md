---
title: Notes - kkopite
display: Notes
subtitle: 一些简短代码片段、笔记
description: 一些简短代码片段、笔记
---

[RSS Feed](https://kkopite.netlify.app/notes/feed.xml)

## 使用`VS Code`编写`vue`时，自定义组件变红，且无法跳转

__2022/05/12__

首先当然是你是使用[`Volar`](https://github.com/johnsoncodehk/volar)这个插件

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
