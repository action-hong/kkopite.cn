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