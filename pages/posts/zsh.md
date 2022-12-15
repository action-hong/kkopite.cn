---
title: window下安装zsh
date: 2022-12-01T09:00:00.000+00:00
lang: zh
duration: 5 min
---

参照[该教程](https://zhuanlan.zhihu.com/p/455925403)

## 一些坑

vscode下终端显示会出现乱码，需要在安装完字体后，配置好终端的字体

```json
{
  "terminal.integrated.fontFamily": "MesloLGS NF"
}
```

可能会出现vscode的终端各种诡异问题：如方向键无法使用、显示光标乱跳、多了很多空格、回退按键各种问题出现，而直接打开`git-bash`一切正常，这时候可以**直接卸载git，重新安装**，基本就能解决了 

使用git的一些乱码（windows下）

使用git的vi编辑器时，中文显示乱码。尝试修改git目录底下的`/path to git/etc/vimrc`

```
set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8
```

使用`git status`显示出来的文件的中文名没有显示正确，尝试如下配置：

```bash
git config --global core.quotepath false
```

使用`git log`时中文也可能出现如下显示错误：

```log
<A1><B1><C1>
```

尝试如下设置：

```bash
git config --global i18n.commitencoding utf-8  # commit 编码
git config --global i18n.logoutputencoding utf-8 # log输出显示编码
```

然后设置环境变量

```
export LESSCHARSET=utf-8
```

> ps: window下可以在环境变量中添加一个`LESSCHARSET`, 对应值为`utf-8`

## 参考

- [git 显示中文和解决中文乱码?- 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/133706032)
- [Linux系统下vim编辑文件时出现中文乱码的解决办法 - 腾讯云开发者社区腾讯(tencent.com)](https://cloud.tencent.com/developer/article/1920588)
- [问题：git处理中文名称时候显示为编码形式（已解决| UTF (lmlphp.com)](https://www.lmlphp.com/user/62605/article/item/2392791/)