---
title: 使用vscode碰到的问题
date: 2022-07-27T15:00:00.000+00:00
lang: zh
duration: 5min
---

[[toc]]

<article>

_2023/07/19_


## 自动引入路径添加 `.js`

假设我们结构如下：

```
- utils.js
- app.js
```

```js
// utils.js
export function foo() {
  return 'hello world'
}
```

在 `app.js` 输入 foo 时会自动提示是否引入，回车后会自动在顶部添加 import 语句：

```js
import { foo } from './utils'
```

但是少了 `.js` 后缀名，如果我们的项目是 `webpack`、`vite` 等工具打包的，它会自动帮我们添加后缀名，但如果是直接用在浏览器上的话就会找不到对应模块，而手动添加 `.js` 又非常麻烦，因此我们可以配置如下：

```json
// .vscode/setting.json
{
  "javascript.preferences.importModuleSpecifierEnding": "js",
}
```

自动写入的语句就会加上后缀名了。

</article>

## 编辑时 tab 键无效，聚焦到其他地方去

大概率是无意中点到了 `ctrl` + `m` 快捷键，即 Toggle Tab Key Move Focus。可以看一下 vscode 的状态栏是否有 **Tab Key Move Focus**，再使用 `ctrl` + `m` 切换一下即可关闭。