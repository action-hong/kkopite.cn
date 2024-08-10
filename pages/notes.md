---
title: Notes - kkopite
display: Notes
subtitle: 一些简短代码片段、笔记
description: 一些简短代码片段、笔记
---

[[toc]]

[RSS Feed](https://kkopite.netlify.app/notes/feed.xml)

<article>

_2024/03/01_

## VS Code 的 node 模块没有提示

很诡异的问题，某个项目里使用`nodejs`的内置模块没有任何提示，似乎是自动安装时出了问题，

尝试手动安装类型包就可以了：

```bash
pnpm add @types/node
```

</article>

<article>

_2024/01/24_

## 原生js方法在react应用中填写input

最近给一个网页写一个油猴脚本，自动填充表单：

```js
const input = document.querySelector('input')
input.value = 'xxx'
input.dispatchEvent(new Event('change', { bubbles: true }))
```

写了如上代码，发现不起作用。

一番搜索，原来该网页是用`React`写的，而`React`里改写了`input.value`的set方法，因此直接`input.value = 'xxx'` 是无效的，解决方案如下：

```js
// https://stackoverflow.com/questions/23892547/what-is-the-best-way-to-trigger-change-or-input-event-in-react-js
const nativeInputValueSetter = Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype, 'value').set
nativeInputValueSetter.call(input, password)

input.dispatchEvent(new Event('change', {
  bubbles: true,
}))
```



</article>

<article>

_2023/12/27_

## `nuxt`初始模板失败

使用官方提供的命令行初始化`nuxt`项目：

```bash
npx nuxi@latest init <project-name>
```

提示如下错误：

```
ERROR  Error: Failed to download template from registry: fetch failed
```

使用[该网站](https://sites.ipaddress.com/raw.githubusercontent.com/)查询`raw.githubusercontent.com`的ip地址，手动去`C://Windows/System32/drivers/etc/hosts`更新：

```
xxx.xxx.xxx.xxx raw.githubusercontent.com 
```

</article>


<article>

_2023/11/28_

## 国际化需要设置`lang`属性

客户反馈日语版本上，「認」显示不对，应该是<span lang="ja" style="font-family: sans-serif">「認」</span>

搜索了一下，原来还需要在标签上设置`lang="ja"`才可以，我们可以在根节点上设置：

```html
<html lang="ja">
    xxxx
</html>
```

> 注意：还需要确保字体支持才行，部分字体即使设置了`lang="ja"`仍然无法显示正确的日语字体

</article>

<article>

_2023/11/07_

## `input` 的 `type="number"` 无效

写一个数字验证码的输入框如下：

```html
<input type="number" />
```

对于 ios 来说无效，而且 PC 上可以输入 `e`，`+`，`-` (因为浮点型的数字是可以由这些[组成的](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#valid-floating-point-number))

> A floating-point number consists of the following parts, in exactly the following order:
>
> 1. Optionally, the first character may be a "-" character.
> 2. One or more characters in the range "0—9".
> 3. Optionally, the following parts, in exactly the following order:
>     1. a "." character
>     2. one or more characters in the range "0—9"
> 4. Optionally, the following parts, in exactly the following order:
>     1. a "e" character or "E" character
>     2. optionally, a "-" character or "+" character
>     3. One or more characters in the range "0—9".

而对于我们数字验证码并不需要浮点型，因此我们只能手动去监听事件，处理这些非数字的字符：

```html
<input type="text" oninput="this.value=this.value.replace(/\D/g, '')">
```

</article>

<article>

_2023/10/27_

## 使用 `jest` 关闭 `prettier`

使用 `jest` 的 inline snapshots 功能时，默认会用 `prettier` 来处理整个文件，可以通过配置 `prettierPath: null` 来关掉它

</article>

<article>

_2023/08/04_

## GitHubError：Resource not accessible by integration (403)

fork 别人的项目提交后，执行 workflow，其中传如下错误：

```bash
Run yarn run gh:release
yarn run v1.22.19
$ conventional-github-releaser -p angular
GitHubError: Resource not accessible by integration (403)
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
Error: Process completed with exit code 1.
```

翻阅[资料](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#modifying-the-permissions-for-the-github_token)，需要修改 GITHUB_TOKEN 的权限：

1. 打开对应仓库的 settings
2. 点击 Actions-General
3. 找到 Workflow permissions 项，选择 **Read and write permissions**
4. 点击 `Save` 即可


</article>

<article>

_2023/06/26_

## keypress 和 keydown 事件中的 `keyCode` 不同

```js
document.addEventListener('keydown', (e) => {
  console.log(e.keyCode)
})
document.addEventListener('keypress', (e) => {
  console.log(e.keyCode)
})
```

尝试点击键盘上的字母，`keydown` 拿到对应英文字母的大写的 ascii 码，而 `keypress` 根据键入的是大写字母还是小写字母拿到对应的 ascii 码

你可以分别尝试键入小写的 `h`，和大写的 `H`

<KeyPress />

参考阅读：

- [javascript - keypress and keyup - why is the keyCode different？ - Stack Overflow](https://stackoverflow.com/questions/11030532/keypress-and-keyup-why-is-the-keycode-different)
- 

</article>

<article>

_2023/04/17_

## 读取配置

一些框架，类库工具都会提供如下的方式来编写配置文件，这样还能借助 ts 的功能来校验配置

```ts
// my.config.ts
import { defineConfig } from 'third-package'

export default defineConfig({

})
```

我们可以使用 `jiti` 这个工具来读取这个 `ts` 配置：

```js
// cli.mjs
import jiti from 'jiti'

// 配置文件为 my.config.ts
// 读取配置
const config = jiti(rootDir, {
  interopDefault: true,
  esmResolve: true,
})('./my.config')
```

或者你可以直接使用这个库 [`unconfig`](https://www.npmjs.com/package/unconfig)


</article>

<article>

_2023/03/15_

## `cordova` 使用本地链接调试

笔者之前开发 `cordova` 时，前端是用 `vue-cli` 工具链开发，可以指定 `config.xml` 内的 `content` 标签值，这样就可以边开发，边直接在手机上看到效果了

这边假设开启的服务跑在如下链接，则可以这样设置

```
// config.xml
<content src="http://192.168.1.245:8080" />
```


</article>


<article>

## clash 规则预处理

_2023/02/06_

我们使用 clash 时，一般都会用自动订阅，这就导致了我们自己加的一些规则在更新时被覆盖掉

我们可以使用 [parsers](https://docs.cfw.lbyczf.com/contents/parser.html#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E9%A2%84%E5%A4%84%E7%90%86)，例如我们添加一个 `openai.com` 的规则，让访问 `chatgpt` 时使用代理：

1. 选择 settings 界面
2. 滚动到 Profiles 栏
3. 点击 Parsers 右侧的 Edit 编辑，输入：
```yaml
parsers:
  - url: 你的订阅地址
    yaml:
      prepend-rules:
        - DOMAIN-SUFFIX,openai.com,Proxies
```

下次自动更新时，这条规则就会自动加上，就不用再每次更新后手动添加规则了。

</article>


<article>

## git 输出中文显示错误

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

然后再次键入 `git status` 就正常了：

```
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1827.最少操作使数组递增.js
```

</article>


<article>

## 透明元素 “遮住” 父元素背景

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

`.container` 元素背景是一条白线，而 `body` 背景是个红到绿的渐变色，因此想让 `.circle` 元素设置背景色来遮住白线就比较困难。

这里可以使用 [`backdrop-filter`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/backdrop-filter) 来实现：

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

> 一开始其实想到一个比较笨的方法，就是通过 [`clip-path`](https://developer.mozilla.org/en-US/docs/Web/CSS/clip-path) 来去裁剪出白线显示的部分，但是如果其 `.circle` 元素位置变化亦或是白线不是横的的话计算就相对麻烦些，遂放弃。

- [效果预览](https://codepen.io/action-hong/pen/BaYYLWW)

</article>

<article>

## `markdown` 代码块跳过 `eslint` 检验

_2022/05/18_


在写 `markdown` 时有时也需要写一些代码，这时候可能会使用 `eslint-plugin-markdown` 来校验这些代码块。但有时候可能代码只是一些示意代码，因此不会那么合法，这时候需要跳过这些代码不进行检验，可以使用 `<!-- eslint-skip -->` 这个注释加载不检测的代码块前面：

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

## 使用 `VS Code` 编写 `vue` 时，自定义组件变红，且无法跳转

_2022/05/12_

首先你使用的是 [`Volar`](https://github.com/johnsoncodehk/volar) 这个插件

在写 `vue` 组件时，经常碰到引入的自定义组件标红高亮且无法进行跳转。一般用以下两种方法就能解决：

1. 更改 `tsconfig.json`，给 `compilerOptions` 配置 `"jsx": "preserve"`：

```json
{
  "compilerOptions": {
    "jsx": "preserve",
  }
}
```

2. 如果你使用 `pnpm`，尝试用 [shamefully-hoist](https://pnpm.io/npmrc#shamefully-hoist) 模式重新安装依赖 (**如果你是使用 `npm` 或 `yarn` 则忽略该方法**)

可以在你的 `.npmrc` 文件里添加如下配置：

```
shamefully-hoist=true
```

然后重新安装：

```bash
pnpm i
```

完成如上步骤后，重新加载 `VS Code`，大概率红色高亮就没了，组件也能正常跳转。

PS：如果还不行只好去官方的 [issue](https://github.com/johnsoncodehk/volar/issues/) 求救了

</article>