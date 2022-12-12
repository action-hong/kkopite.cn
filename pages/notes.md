---
title: Notes - kkopite
display: Notes
subtitle: 一些简短代码片段、笔记
description: 一些简短代码片段、笔记
---

[[toc]]

[RSS Feed](https://kkopite.netlify.app/notes/feed.xml)

<article>

## git输出中文显示错误

_2022/12/12_

我们给仓库里面添加一个中文文件，然后查看时，发现中文文字没有很好的显示：

```bash
git status
```

```log
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   "1827.\346\234\200\345\260\221\346\223\215\344\275\234\344\275\277\346\225\260\347\273\204\351\200\222\345\242\236.js"
```

这时候可尝试做如下配置：

```bash
git config --global core.quotepath false
```

然后再次键入`git status`就正常了：

```
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1827.最少操作使数组递增.js
```

</article>


<article>

## 透明元素“遮住”父元素背景

_2022/05/28_

我们需要实现类似下图的效果：

![transparent-element-hide-bg](/images/transparent-element-hide-bg.png)

它的结构是这样的：

```html
<body>
  <div class="container">
    <div class="circle">1</div>
    <div class="circle">2</div>
    <div class="circle">3</div>
  </div>
</body>
```

`.container`元素背景是一条白线，而`body`背景是个红到绿的渐变色，因此想让`.circle`元素设置背景色来遮住白线就比较困难。

这里可以使用[`backdrop-filter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/backdrop-filter)来实现：

```diff
.circle {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 2px solid #fff;
  color: #fff;
  line-height: 40px;
  text-align: center;
+ backdrop-filter: blur(10px);
}
```

> 一开始其实想到一个比较笨的方法，就是通过[`clip-path`](https://developer.mozilla.org/en-US/docs/Web/CSS/clip-path) 来去裁剪出白线显示的部分，但是如果其`.circle`元素位置变化亦或是白线不是横的的话计算就相对麻烦些，遂放弃。

- [效果预览](https://codepen.io/action-hong/pen/BaYYLWW)

</article>

<article>

## `markdown`代码块跳过`eslint`检验

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