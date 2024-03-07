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
      desc: '一款可以查看ip地址的 VS Code 插件'
      icon: 'i-iconoir-ip-address'
    - name: 'VS Code zhlint Extension'
      link: 'https://github.com/zhlint-project/zhlint-vscode'
      desc: '一款zhlint的 VS Code 插件'
    - name: 'VS Code Tip'
      link: 'https://github.com/action-hong/vscode-tip'
      desc: '使用 corn 配置定时提醒，提醒喝水等等'
    - name: 'i18n kk'
      link: 'https://github.com/action-hong/i18n-ally'
      desc: '基于 i18n ally 添加可配置术语表功能' 
---

<ListProjects :projects="frontmatter.projects"/>
