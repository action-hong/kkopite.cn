---
title: 项目 - kkopite
display: Projects
subtitle: 一些平时做的小工具，小项目
description: 一些平时做的小工具，小项目
projects:
  CSS:
    - name: 'unocss-preset-scrollbar'
      link: 'https://github.com/action-hong/unocss-preset-scrollbar'
      desc: 'unocss的滚动条预设'
      icon: 'unocss'
  Game:
    - name: 'Arithmetic 猜猜猜'
      link: 'https://arithmetic-game.netlify.app/'
      desc: '四则运算版本的Wordle'
      icon: 'i-bx-math'
  VS Code Extensions:
    - name: 'Show IP Address'
      link: 'https://github.com/action-hong/vscode-show-ip'
      desc: '一款可以查看ip地址的vscode插件'
      icon: 'i-iconoir-ip-address'
---

<ListProjects :projects="frontmatter.projects"/>
