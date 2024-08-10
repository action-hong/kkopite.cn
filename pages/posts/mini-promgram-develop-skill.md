---
title: 小程序开发的一些tips
date: 2024-08-10T23:00:00.000+00:00
lang: zh
duration: 10 min
---

## 自定义导航栏的高度计算

```js
const sysInfo = wx.getWindowInfo()
const menuInfo = wx.getMenuButtonBoundingClientRect()
// 导航栏高度
const navigationBarHeight = (menuInfo.top - sysInfo.statusBarHeight) * 2 + menuInfo.height
// 状态栏高度
const statusBarHeight = sysInfo.statusBarHeight
```